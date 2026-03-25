<script setup>
import { ref, watch, onMounted } from 'vue'

import Login from './components/Login.vue'
import Dashboard from './components/Dashboard.vue'
import Sidebar from './components/Sidebar.vue'
import ScreenResume from './components/ScreenResume.vue'
import UploadResume from './components/UploadResume.vue'

// AUTH
const loggedIn = ref(false)

// NAV
const page = ref('dashboard')

// GLOBAL DATA
const savedJobs = ref([])
const savedResumes = ref([])

// LOAD jobs from localStorage on startup
onMounted(() => {
  const data = localStorage.getItem('jobs')
  if (data) {
    savedJobs.value = JSON.parse(data)
  }
})

// SAVE jobs whenever they change
watch(savedJobs, (newJobs) => {
  localStorage.setItem('jobs', JSON.stringify(newJobs))
}, { deep: true })

// LOGIN HANDLER
function handleLogin(success) {
  if (success) loggedIn.value = true
}
</script>

<template>
  <Login 
    v-if="!loggedIn" 
    @loginSuccess="handleLogin" 
  />

  <div v-else class="layout">
    
    <Sidebar @navigate="page = $event" />

    <Dashboard 
      v-if="page === 'dashboard'" 
      :savedJobs="savedJobs"
    />

    <UploadResume
     v-if="page === 'resume'" 
      :resumes="savedResumes"
    />

    <ScreenResume 
      v-if="page === 'screen'" 
      :jobs="savedJobs"
      :resumes="savedResumes"
    />

  </div>
</template>

<style>
.layout {
  display: flex;
  height: 100vh;
  width: 100%;
}
</style>