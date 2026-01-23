<script setup>
import { ref, useTemplateRef } from 'vue'

let regexStr = ref('')
const text = useTemplateRef('text')
const results = ref([])

function highlightMatches() {
  const regex = new RegExp(regexStr.value, 'gi')

  results.value = text.value.innerText.match(regex)
  console.log(results.value)
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