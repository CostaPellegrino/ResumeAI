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
    <a>Candidates</a>
    <a @click="emit('navigate','resume')">Upload Resume</a>
    <a>Upload Job Posting</a>
    <a @click="emit('navigate','screen')">Screen Resume</a>
  </nav>

</aside>
</template>