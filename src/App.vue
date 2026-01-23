<script setup>
import { ref, useTemplateRef } from 'vue'

let regexStr = ref('')
const text = useTemplateRef('text')
const results = ref([])

function highlightMatches() {
  const regex = new RegExp(regexStr.value, 'gi')
  
  results.value = text.value.innerText.match(regex)
  console.log(results.value)
  
  const walker = document.createTreeWalker(document.body, NodeFilter.SHOW_TEXT, {
    acceptNode: (node) => {
      // Skip text nodes in script, style, and other non-content elements
      const parent = node.parentElement;
      if (!parent || 
      parent.tagName === 'SCRIPT' || 
      parent.tagName === 'STYLE' || 
      parent.tagName === 'NOSCRIPT' ||
      parent.classList.contains('chrome-regex-highlight') ||
      parent.closest('.chrome-regex-search')) {
        return NodeFilter.FILTER_REJECT;
      }
      
      // // For non-HTML content (plain text files), prioritize pre elements
      // if (hasPreContent && !hasStandardContent) {
      //   if (parent.tagName === 'PRE') {
      //     return NodeFilter.FILTER_ACCEPT;
      //   }
      //   return NodeFilter.FILTER_REJECT;
      // }
      
      // For HTML pages, skip navigation and footer elements to focus on main content
      // This prevents matches in menus, headers, footers, and UI elements
      if (parent.closest('header, nav, footer, .printfooter, [role="navigation"], [role="banner"], [role="contentinfo"]')) {
        return NodeFilter.FILTER_REJECT;
      }
      
      // Also check if parent itself is a navigation element
      const navClasses = ['vector-dropdown', 'vector-menu', 'vector-header', 'navigation', 'navbar'];
      if (parent.className && typeof parent.className === 'string') {
        if (navClasses.some(cls => parent.className.includes(cls))) {
          return NodeFilter.FILTER_REJECT;
        }
      }
      
      // GENERAL: Skip elements inside CSS-hidden containers (all sites)
      // This prevents finding matches in invisible content that can't be scrolled to
      // Examples: Wikipedia's "Hidden categories", collapsed sections, etc.
      const computedStyle = window.getComputedStyle(parent);
      if (computedStyle.display === 'none' || computedStyle.visibility === 'hidden') {
        return NodeFilter.FILTER_REJECT;
      }
      
      // Also check all ancestors for hidden state (handles nested hidden elements)
      let ancestor = parent.parentElement;
      while (ancestor && ancestor !== document.body) {
        const ancestorStyle = window.getComputedStyle(ancestor);
        if (ancestorStyle.display === 'none' || ancestorStyle.visibility === 'hidden') {
          return NodeFilter.FILTER_REJECT;
        }
        ancestor = ancestor.parentElement;
      }
      
      // YouTube-specific filtering to match Chrome's native search behavior
      // YouTube's complex DOM includes many hidden UI elements that shouldn't be searched
      const isYouTube = window.location.hostname.includes('youtube.com');
      
      if (isYouTube) {
        // 1. Skip elements with display:none or visibility:hidden
        const style = window.getComputedStyle(parent);
        if (style.display === 'none' || style.visibility === 'hidden') {
          return NodeFilter.FILTER_REJECT;
        }
        
        // 2. Skip elements with hidden attribute
        if (parent.hasAttribute('hidden') || parent.closest('[hidden]')) {
          return NodeFilter.FILTER_REJECT;
        }
        
        // 3. Skip aria-hidden elements (tooltips, overlays, etc.)
        if (parent.getAttribute('aria-hidden') === 'true' || 
        parent.closest('[aria-hidden="true"]')) {
          return NodeFilter.FILTER_REJECT;
        }
        
        // 4. Skip YouTube player controls and UI components
        const ytUIClasses = [
        'ytp-',                   // YouTube player controls
        'ytd-menu-',              // Dropdown menus
        'yt-dropdown',            // Dropdowns
        'tp-yt-paper-tooltip',    // Tooltips
        'yt-tooltip',             // More tooltips
        'yt-chip-cloud',          // Filter chips (when collapsed)
        'ytd-engagement-panel',   // Side panels (hidden by default)
        ];
        
        if (parent.className && typeof parent.className === 'string') {
          if (ytUIClasses.some(cls => parent.className.includes(cls))) {
            return NodeFilter.FILTER_REJECT;
          }
        }
        
        // 5. Skip if parent is inside player overlays, dropdowns, or menus
        if (parent.closest('.ytp-chrome-top, .ytp-chrome-bottom, .ytp-gradient-top, .ytp-gradient-bottom, tp-yt-paper-menu-button, yt-dropdown-menu, ytd-menu-renderer, ytd-popup-container')) {
          return NodeFilter.FILTER_REJECT;
        }
        
        // 6. Skip elements with zero dimensions (truly hidden/collapsed)
        const rect = parent.getBoundingClientRect();
        if (rect.width === 0 || rect.height === 0) {
          return NodeFilter.FILTER_REJECT;
        }
      }
      
      // Only process text nodes with actual content
      return node.textContent.trim().length > 0 
      ? NodeFilter.FILTER_ACCEPT 
      : NodeFilter.FILTER_REJECT;
    }
  })

  console.log(results.value[0])

  // console.log(walker.nextNode().textContent.test(regex))
  console.log(regex.test(walker.nextNode().textContent))

  // for (let i = 0; i < results.value.length; i++) {
  //   const curMatch = results.value[0]

  //   do {

  //   } while (!walker.nextNode.test(regex))
  // }
}
</script>

<template>
  <main>
    <section>
      <input ref="input" v-model="regexStr" @input="highlightMatches">
    </section>
    
    <section ref="text">
      +1 to All Skills<br>
      +2 to All Skills<br>
      
      +3 to Rabies<br>
      +3 to Cold Skills<br>
      +4 to Fire Skills<br>
      +4 to Corpse Exp<br>
      +2 to Assasinate<br>
    </section>
  </main>
</template>