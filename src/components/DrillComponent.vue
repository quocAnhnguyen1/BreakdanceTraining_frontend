<script setup>
import { ref } from 'vue'
const time = ref(40)
const phase = ref("Ready")
const round = ref(1)

let interval = null
let isRunning = false

function start() {
  if (isRunning) return
  isRunning = true
  round.value = 1
  startWork()
}

function startWork() {
  phase.value = "Work"
  time.value = 40
  runTimer(() => {
    if (round.value < 3) {
     startRest()
    } else {
     phase.value = "Finish"
      isRunning = false
    }
    saveSession()
  })
}

function startRest() {
  phase.value = "Rest"
  time.value = 20

  runTimer(() => {
    round.value++
    startWork()
  })
}


function runTimer(callback) {
  clearInterval(interval)   //
  interval = setInterval(() => {
    if (time.value > 0) {
      time.value--
    } else {
      clearInterval(interval)
      callback()
    }
  }, 1000)
}


function stop() {
  clearInterval(interval)
  isRunning = false
  phase.value = "Paused"
}


function continueSession() {
  if (isRunning) return
  isRunning = true
  runTimer(() => {
    if (phase.value === "Work") {
      startRest()
    } else {
      round.value++
      startWork()
    }
  })
}


function reset() {
  clearInterval(interval)
  isRunning = false
  time.value = 40
  phase.value = "Ready"
  round.value = 1
}

async function saveSession() {
  await fetch("http://localhost:8080/sessions", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      workTime: 40,
      restTime: 20,
      round: 3
    })
  })
}
</script>


<template>
  <div>
    <h2>Drill Session</h2>
    <h1>{{ time }}</h1>
    <p>{{ phase }} - Round {{ round }}</p>
    <button @click="start">Start</button>
    <button @click="stop">Stop</button>
    <button @click="continueSession">Continue</button>
    <button @click="reset">Reset</button>
  </div>
</template>
