<template>
  <div class="wrapper w-full min-h-screen bg-[#F2FFF5]">
    <div class="container mx-auto grid place-items-center h-screen">
      <div class="timer">
        <div
          v-if="status.isPomodoro"
          class="status py-[10px] px-[16px] flex items-center justify-center gap-5 rounded-full border-2 bg-[#FF4C4C26] border-[#471515] text-[#471515]"
        >
          <Icons name="status-brain" color="#471515" />

          <span>Focus</span>
          <span>{{ focusPomodoros.total }}</span>
        </div>

        <div
          v-else-if="status.isShortBreak"
          class="status py-[10px] px-[16px] flex items-center justify-center gap-5 rounded-full border-2 bg-[#4DDA6E26] border-[#14401d] text-[#14401d]"
        >
          <Icons name="status-coffee" color="#14401d" />

          <span>Short Break</span>
          <span>{{ focusPomodoros.total }}</span>
        </div>

        <div
          v-else-if="status.isLongBreak"
          class="status py-[10px] px-[16px] flex items-center justify-center gap-5 rounded-full border-2 bg-[#4CACFF26] border-[#153047] text-[#153047]"
        >
          <Icons name="status-coffee" color="#153047" />

          <span>Long Break</span>
          <span>{{ focusPomodoros.total }}</span>
        </div>

        <div class="numbers select-none">
          <h1>{{ minute < 10 ? '0' : '' }}{{ minute }}</h1>
          <h1>{{ second < 10 ? '0' : '' }}{{ second }}</h1>
        </div>

        <div class="controls flex gap-3 text-4xl">
          <button class="p-6 bg-[rgba(77,218,110,0.15)] rounded-3xl">
            <i class="bx bx-dots-horizontal-rounded"></i>
          </button>

          <button
            @click="control"
            :disabled="isStopped"
            class="p-6 bg-[rgba(77,218,110,0.15)] rounded-3xl"
          >
            <i v-if="!isRunning" class="bx bx-play"></i>
            <i v-else class="bx bx-pause"></i>
          </button>

          <button @click="next" class="p-6 bg-[rgba(77,218,110,0.15)] rounded-3xl">
            <i class="bx bx-skip-next"></i>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import Icons from './components/Icons.vue'
import { ref, reactive, computed, onMounted } from 'vue'

const focusPomodoros = reactive({
  total: 0,
  limit: 4
})

const status = reactive({
  isPomodoro: true,
  isShortBreak: false,
  isLongBreak: false
})

const pomodoroLength = reactive({
  minute: 25,
  second: 0
})

const shortBreakLength = reactive({
  minute: 5,
  second: 0
})

const longBreakLength = reactive({
  minute: 30,
  second: 0
})

const minute = ref(pomodoroLength.minute)
const second = ref(pomodoroLength.second)

const timer = ref(null)
const isRunning = ref(false)

const isStopped = computed(() => {
  if (minute.value == 0 && second.value == 0) {
    stop()
    return true
  } else {
    return false
  }
})

const start = () => {
  if (!isRunning.value) {
    timer.value = setInterval(() => {
      if (minute.value > 0) {
        second.value -= 1
        if (second.value < 0) {
          second.value = 59
          minute.value -= 1
        }
      } else if (minute.value == 0) {
        if (second.value > 0) {
          second.value -= 1
          if (second.value == 0) {
            stop()
          }
        }
      }
    }, 1000)
    isRunning.value = true
  }
}

const stop = () => {
  if (isRunning.value) {
    clearInterval(timer.value)
    isRunning.value = false
  }
}

const next = () => {
  if (status.isPomodoro) {
    focusPomodoros.total++
    if (focusPomodoros.total % focusPomodoros.limit != 0) {
      minute.value = shortBreakLength.minute
      second.value = shortBreakLength.second
      status.isShortBreak = true
      status.isPomodoro = false
    } else {
      minute.value = longBreakLength.minute
      second.value = longBreakLength.second
      status.isLongBreak = true
      status.isPomodoro = false
    }
  } else {
    minute.value = pomodoroLength.minute
    second.value = pomodoroLength.second
    status.isPomodoro = true
    status.isShortBreak = false
    status.isLongBreak = false
  }
  stop()
}

const control = () => {
  if (isRunning.value) {
    stop()
  } else {
    start()
  }
}

onMounted(() => {})
</script>

<style lang="scss" scoped>
.timer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.controls {
  height: 100px;
  display: flex;
  align-items: center;
}

.controls button {
  transition: all 0.3s ease;
}

.controls button:focus {
  padding: 32px 48px;
  background: rgba(77, 218, 110, 0.62);
}

.controls button:hover {
  background: #86e299;
}

.numbers {
  color: #14401d;
  font-weight: 800;
  font-size: 256px;
  line-height: 85%;
  margin: 32px 0;
}
</style>
