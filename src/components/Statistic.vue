<script setup>
import { ref, onMounted } from 'vue'

const stats = ref([])

onMounted(async () => {
  const res = await fetch("http://localhost:8080/sessions")
  const data = await res.json()
  stats.value = data.map(s => ({
    date: "today",
    count: s.round
  }))
})
</script>

<template>
  <div>
    <h2>Statistics</h2>

    <div v-if="stats.length === 0">
      No data available
     </div>


    <div v-for="day in stats" :key="day.date">
    {{ day.date }} : {{ day.count }} sessions
    </div>
    </div>
</template>
