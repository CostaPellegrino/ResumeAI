<script setup>
const emit = defineEmits(['navigate'])
</script>

<template>
<aside class="sidebar">

  <div class="logo">
    <h2>Smart Hire</h2>
    <p>Recruitment Dashboard</p>
  </div>

  <nav>
    <a @click="emit('navigate','dashboard')">Overview</a>
    <a @click="emit('navigate','candidate')">Candidates</a>
    <a @click="emit('navigate','resume')">Upload Resume</a>
    <a @click="emit('navigate','job')">Upload Jobs</a>
    <a @click="emit('navigate','screen')">Screen Resume</a>
  </nav>

</aside>
</template>