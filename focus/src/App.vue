<script setup>
import Timer from "./components/Timer.vue";

// import HelloWorld from './components/HelloWorld.vue'
// import TheWelcome from './components/TheWelcome.vue'
</script>

<template>
  <h3>Сначала тест таймера</h3>
  <Timer
    :workTime="{ minutes: 10, seconds: 0 }"
    :freeTime="{ minutes: 5, seconds: 0 }"
    :times="1"
  />
</template>

<style scoped lang="sass">

h3
  color: brown
  margin-bottom: 50px
</style>
