<script>
import { defineComponent } from "vue";
import { clearInterval, setInterval } from "worker-timers";

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
      function beep(freq) {
        const ctx = new (window.AudioContext || window.webkitAudioContext)();
        let osc = ctx.createOscillator();
        osc.connect(ctx.destination);
        osc.frequency.value = freq;
        osc.type = "sine";
        osc.start();
        osc.stop(ctx.currentTime + 0.4);
      }
      beep(800);
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
                beep(800);
                this.startTimer();
              } else if (this.nowTime == 0) {
                beep(800);
                this.nowTime = null;
                this.runMin = null;
                this.runSec = null;
                this.isfreeTime = !this.isfreeTime;
              }
              clearInterval(time);
            }
          }
        }, 1000);
      }
      beep(800);
    },
    pause() {
      this.isPause = !this.isPause;
    },
  },
});
</script>

<template>
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
