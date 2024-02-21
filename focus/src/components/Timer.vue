<script>
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      minutes: 0,
      seconds: 0,
      runMin: null,
      runSec: null,
      isPause: false,
      nowTime: null,
      isfreeTime: false,
    };
  },
  props: {
    times: {
      type: Number,
    },
    workTime: {
      type: Object,
    },
    freeTime: {
      type: Object,
    },
  },
  methods: {
    startTimer() {
      console.log("Старт таймера");
      if (this.nowTime == null) {
        this.nowTime = this.times * 2 - 1;
      }

      // переменная для сброса
      console.log(this.runMin);
      let clear = false;
      if (this.runMin !== null) {
        console.log("Начало сброса");
        clear = true;
      }

      this.isPause = false;

      if (!this.isfreeTime) {
        this.runMin = this.workTime.minutes;
        this.runSec = this.workTime.seconds;
      } else {
        this.runMin = this.freeTime.minutes;
        this.runSec = this.freeTime.seconds;
      }

      if (this.runMin > 0 || this.runSec > 0) {
        let time = setInterval(() => {
          if (clear) {
            this.runMin = null;
            // сброс интервала, если таймер уже идет
            clearInterval(time);
            // Костыль для фикса бага
            this.runSec += 1;
            clear = false;

            console.log(this.runMin);
            console.log("Сброс таймера в случае бага");
          }

          if (this.isPause == false) {
            if (this.runSec != 0) {
              this.runSec -= 1;
            } else if (this.runSec == 0 && this.runMin > 0) {
              this.runMin -= 1;
              this.runSec = 59;
            } else if (this.runSec == 0 && this.runMin == 0) {
              if (this.nowTime != 0) {
                this.nowTime -= 1;
                this.runMin = null;

                this.isfreeTime = !this.isfreeTime;
                console.log("Старт следующего цикла");
                this.startTimer();
              } else if (this.nowTime == 0) {
                this.nowTime = null;
              }
              clearInterval(time);
            }
          }
        }, 1000);
      }
    },
    pause() {
      this.isPause = !this.isPause;
    },
  },
});
</script>

<template>
  <input v-model="minutes" type="number" /> :
  <input v-model="seconds" type="number" />
  <button @click="startTimer">start</button>

  <div class="timer">
    <p v-if="isfreeTime">Идет перерыв</p>
    <p v-else>Рабочее время</p>

    <p>
      Минуты <span class="min"> {{ runMin }} : </span> Секунды
      <span class="sec">{{ runSec }}</span>
    </p>
  </div>
  <button @click="pause">Пауза</button>

  <div :class="isfreeTime ? 'free' : 'work'" class="block"></div>
</template>

<style scoped lang="sass">
input
  width: 50px
  margin-bottom: 50px
  margin-left: 10px
  font-size: 20px

.timer
  font-size: 22px

.block
  width: 500px
  height: 300px

.work
  background-color: blue

.free
  background-color: green
</style>
