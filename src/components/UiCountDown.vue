<template>
  <div ref="counter" class="UiCountDown counter">
    <div class="counter-element">
      <div class="counter-element-time days">
        <div class="">{{ days[0] }}</div>
        <div class="">{{ days[1] }}</div>
      </div>
      <p class="counter-element-text">Дней</p>
    </div>
    <div class="counter-element">
      <div class="counter-element-time hours">
        <div class="">{{ hours[0] }}</div>
        <div class="">{{ hours[1] }}</div>
      </div>
      <p class="counter-element-text">Часов</p>
    </div>
    <div class="counter-element">
      <div class="counter-element-time minutes">
        <div class="">{{ minutes[0] }}</div>
        <div class="">{{ minutes[1] }}</div>
      </div>
      <p class="counter-element-text">Минут</p>
    </div>
    <div class="counter-element">
      <div class="counter-element-time seconds">
        <div class="">{{ seconds[0] }}</div>
        <div class="">{{ seconds[1] }}</div>
      </div>
      <p class="counter-element-text">Секунды</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from 'vue'

const props = defineProps({
  endDate: {
    type: String,
    default: '2024-09-29 07:00'
  }
})
let counter = ref(null)
let timer: any = null

let days = ref(['0', '0'])
let hours = ref(['0', '0'])
let minutes = ref(['0', '0'])
let seconds = ref(['0', '0'])
const endDate = new Date(props.endDate).getTime()

function formatTime(time: number) {
  const d = Math.floor(time / (1000 * 60 * 60 * 24))
  const h = Math.floor((time % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
  const m = Math.floor((time % (1000 * 60 * 60)) / (1000 * 60))
  const s = Math.floor((time % (1000 * 60)) / 1000)
  return { d, h, m, s }
}

const getDigits = (num: number): string[] => {
  let str = `${num}`
  str = str.length === 1 ? '0' + num : str
  let first = str[0]
  let second = str[1]
  return [first, second]
}

function updateCountdown() {
  console.log('updateCountdown')
  const now = new Date().getTime()

  const remainingTime = Math.max(0, endDate - now)
  const { d, h, m, s } = formatTime(remainingTime)

  days.value = getDigits(d)
  hours.value = getDigits(h)
  minutes.value = getDigits(m)
  seconds.value = getDigits(s)
}

function startCountDown() {
  timer = setInterval(updateCountdown, 1000)
}
function stopCountDown() {
  clearInterval(timer)
}

function handleIntersect(event: any) {
  if (event[0].isIntersecting) startCountDown()
  else stopCountDown()
}

function createObserver() {
  let observer
  let counterEl = counter.value

  let options = {
    root: null,
    rootMargin: '200px',
    threshold: 0.1
  }

  observer = new IntersectionObserver(handleIntersect, options)
  if (counterEl) observer.observe(counterEl)
}

onMounted(() => {
  createObserver()
  updateCountdown()
})
onUnmounted(() => {
  stopCountDown()
})
</script>

<style scoped>
::root {
  --size: 16px;
  --width: 50px;
}

.counter {
  width: 80%;
  height: 40%;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  align-items: center;
  gap: 5%;
}
.counter-element {
  width: 100%;
  height: 100%;
}
.counter-element-time {
  width: 100%;
  height: 60%;
  display: flex;
  gap: 5px;
}
.counter-element-time div {
  width: 50%;

  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.1);

  border-radius: 8px;

  font-size: 50px;
  font-weight: 600;
}
.counter-element-text {
  margin-top: 4px;
  text-align: center;
  font-size: 18px;
  font-weight: 600;
  line-height: 22px;
}
@media (max-width: 768px) {
  .counter-element {
    margin-right: 5px;
  }
  .counter-element-time {
    gap: 2px;
  }
  .counter-element-time div {
    height: 42px;
    width: 35px;

    font-size: 24px;
    line-height: 28px;
  }
  .counter-element-text {
    font-size: 14px;
    line-height: 16px;
  }
}
</style>
