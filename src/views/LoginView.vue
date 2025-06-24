<template>
  <div style="max-width: 300px; margin: 100px auto;">
    <h2>Login</h2>
    <form @submit.prevent="handleLogin">
      <div>
        <label>Email:</label>
        <input v-model="email" type="email" required />
      </div>
      <div style="margin-top: 10px;">
        <label>Password:</label>
        <input v-model="password" type="password" required />
      </div>
      <button type="submit" style="margin-top: 15px;">Login</button>
    </form>
    <p v-if="error" style="color: red; margin-top: 10px;">{{ error }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const email = ref('')
const password = ref('')
const error = ref('')
const router = useRouter()

function handleLogin() {
  if (email.value === 'admin@gmail.com' && password.value === 'admin123') {
    // Store login status in localStorage
    localStorage.setItem('isLoggedIn', 'true')
    error.value = ''
    router.push('/dashboard') // redirect on success
  } else {
    error.value = 'Invalid email or password.'
  }
}
</script>
