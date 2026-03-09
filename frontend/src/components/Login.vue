<script setup>
import { ref } from 'vue'

const emit = defineEmits(['loginSuccess'])

const username = ref('')
const password = ref('')
const error = ref('')

function login() {

  if (username.value === 'admin' && password.value === 'admin') {
    emit('loginSuccess', true)
  } else {
    error.value = 'Invalid username or password'
  }

}
</script>

<template>
  <div class="login-wrapper">

    <div class="login-card">

      <h1>Login</h1>

      <input
        v-model="username"
        placeholder="Username"
        class="input"
      />

      <input
        v-model="password"
        type="password"
        placeholder="Password"
        class="input"
      />

      <button @click="login" class="login-btn">
        Login
      </button>

      <p v-if="error" class="error">{{ error }}</p>

    </div>

  </div>
</template>