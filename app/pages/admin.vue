<script setup>
definePageMeta({
  layout: 'blank' // ensures no sidebar/navbar
})

import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const password = ref('')
const rememberMe = ref(false)
const error = ref('')

// Hardcoded password for demo (replace with real auth)
const ADMIN_PASSWORD = 'admin123'

// Admin login function
const loginAsAdmin = () => {
  if (password.value === ADMIN_PASSWORD) {
    if (rememberMe.value) {
      localStorage.setItem('isAdminLoggedIn', 'true')
    } else {
      sessionStorage.setItem('isAdminLoggedIn', 'true')
    }
    router.push('/') // redirect to dashboard
  } else {
    error.value = 'Invalid password'
  }
}

// Google login (frontend demo only)
const loginWithGoogle = () => {
  alert('Google login clicked! Implement OAuth backend to proceed.')
}
</script>

<template>
  <div class="flex justify-center items-center min-h-screen bg-black text-gray-100">

    <!-- LOGIN BOX -->
    <div class="bg-gray-900 p-10 sm:p-8 px-6 rounded-2xl shadow-2xl w-full max-w-md border border-gray-800">

      <h1 class="text-3xl font-bold mb-6 text-center text-white">
        Admin Login
      </h1>

      <!-- PASSWORD INPUT -->
      <input
        type="password"
        v-model="password"
        placeholder="Enter admin password"
        class="w-full bg-gray-800 text-white px-4 py-3 rounded-xl mb-2 border border-gray-700 focus:outline-none focus:ring-2 focus:ring-orange-500"
      />

      <!-- REMEMBER ME -->
      <label class="flex items-center gap-2 mb-4 text-gray-300">
        <input type="checkbox" v-model="rememberMe" class="w-4 h-4 rounded focus:ring-orange-500"/>
        Remember Me
      </label>

      <!-- ERROR MESSAGE -->
      <p v-if="error" class="text-red-500 mb-4">{{ error }}</p>

      <!-- LOGIN BUTTON -->
      <button 
        type="button"
        @click="loginAsAdmin"
        aria-label="Login as admin"
        class="w-full bg-orange-600 hover:bg-orange-700 text-white px-4 py-3 rounded-xl 
               transition duration-300 font-semibold shadow-lg
               focus:outline-none focus:ring-2 focus:ring-orange-500 focus:ring-offset-1 mb-4"
      >
        Login
      </button>

      <!-- OR separator -->
      <div class="flex items-center gap-2 my-4">
        <hr class="flex-1 border-gray-700"/>
        <span class="text-gray-400 text-sm">OR</span>
        <hr class="flex-1 border-gray-700"/>
      </div>
    </div>
  </div>
</template>