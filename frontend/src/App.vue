<script setup>
import { ref } from 'vue'
import Login from './components/Login.vue'

const loggedIn = ref(false)

function handleLogin(success) {
  if (success) loggedIn.value = true
}
</script>

<template>
  <Login v-if="!loggedIn" @loginSuccess="handleLogin" />
  <div v-else class="dashboard">
    <h1>Dashboard</h1>
    <p>You are logged in.</p>
  </div>
</template>

<style>
.dashboard{
  height:100vh;
  display:flex;
  justify-content:center;
  align-items:center;
  font-size:28px;
}
</style>