<script setup>
import { ref } from "vue";
import Timer from "./components/Timer.vue";

const workmin = ref(0);
const worksec = ref(0);
const freemin = ref(0);
const freesec = ref(0);
const repeat = ref(1);
</script>

<template>
  <div class="input-container">
    <div class="time-item">
      <p>Время работы</p>
      <div class="time-input">
        <input v-model.number="workmin" type="number" />
        :
        <input v-model.number="worksec" type="number" />
      </div>
    </div>
    <div class="time-item">
      <p>Время отдыха</p>
      <div class="time-input">
        <input v-model.number="freemin" type="number" />
        :
        <input v-model.number="freesec" type="number" />
      </div>
    </div>
    <input v-model.number="repeat" type="number" />
  </div>
  <Timer
    :workTime="{ minutes: workmin, seconds: worksec }"
    :freeTime="{ minutes: freemin, seconds: freesec }"
    :times="repeat"
  />
</template>

<style scoped lang="sass">

h3
  color: brown
  margin-bottom: 50px

.input-container
  display: flex
  gap: 30px
  align-items: flex-end
  justify-content: center
  margin: 50px

input
  width: 50px
  font-size: 20px
</style>
