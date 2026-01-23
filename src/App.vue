<script setup>
import { ref, useTemplateRef } from 'vue'

const regexInputEl = useTemplateRef('input')
let regexInput = ref('')
const text = useTemplateRef('text')
const results = ref([])

function highlightMatches() {
  const regex = new RegExp(regexInput.value, 'gi')

  console.log(text.value.innerText.match(regex))
  results.value = text.value.innerText.match(regex)
}

function test() {
  // console.log(regex.value)
  highlightMatches()

  console.log(regexInputEl.value)
}
</script>

<template>
  <main>
    <section>
      <input ref="input" v-model="regexInput" @input="test">
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