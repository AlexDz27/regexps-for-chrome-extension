<script setup>
import { ref, useTemplateRef } from 'vue'

const text = useTemplateRef('text')

setTimeout(() => {
  console.log(text.value.textContent)
  console.log(text.value.textContent.match(/\+\d to (?!All).*/g))
})
</script>

<template>
  <main>
    <section>
      <input id="input">
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