<script setup>
import { ref } from 'vue'

let regex = ref('')

function test() {
  console.log(regex.value)
}
</script>

<template>
  <main>
    <section>
      <input id="input" v-model="regex" @input="test">
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