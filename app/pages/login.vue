<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

// Admin credentials (dummy)
const adminUser = {
  email: 'admin@kvda.go.ke',
  password: 'Admin@123'
}

// Form state
const email = ref('')
const password = ref('')
const error = ref('')

const regEmail = ref('')
const regPassword = ref('')
const regPasswordConfirm = ref('')
const regError = ref('')

const resetEmail = ref('')
const resetMessage = ref('')

// Active tab
const activeTab = ref('login')

// --------- Login Function ---------
function login() {
  const e = email.value.trim()
  const p = password.value.trim()

  if (!e || !p) {
    error.value = 'Please enter email and password'
    return
  }

  // Check against dummy admin credentials
  if (e === adminUser.email && p === adminUser.password) {
    // Successful login
    email.value = ''
    password.value = ''
    error.value = ''

    // Redirect to dashboard
    router.push('/') // Replace '/' with '/dashboard' if your route differs
  } else {
    error.value = 'Invalid email or password'
  }
}

// --------- Register Function ---------
function register() {
  if (!regEmail.value || !regPassword.value || !regPasswordConfirm.value) {
    regError.value = 'Please fill all fields'
    return
  }
  if (regPassword.value !== regPasswordConfirm.value) {
    regError.value = 'Passwords do not match'
    return
  }

  alert('Account created for ' + regEmail.value)
  regEmail.value = ''
  regPassword.value = ''
  regPasswordConfirm.value = ''
  regError.value = ''
  activeTab.value = 'login'
}

// --------- Reset Password Function ---------
function resetPassword() {
  if (!resetEmail.value) return
  resetMessage.value = `Reset link sent to ${resetEmail.value}`
  resetEmail.value = ''
}
</script>

<template>
  <div class="flex justify-center items-center min-h-screen bg-black text-gray-100">

    <div class="bg-gray-900 p-10 sm:p-8 px-6 rounded-2xl shadow-2xl w-full max-w-md border border-gray-800">

      <h1 class="text-3xl font-bold mb-8 text-center text-white">Admin Login</h1>

      <!-- Tabs -->
      <div class="flex justify-center space-x-4 text-cyan-400 font-semibold mb-6">
        <button @click="activeTab='login'" :class="{'underline': activeTab==='login'}">Login</button>
        <button @click="activeTab='register'" :class="{'underline': activeTab==='register'}">Create Account</button>
        <button @click="activeTab='reset'" :class="{'underline': activeTab==='reset'}">Reset Password</button>
      </div>

      <!-- Login Form -->
      <div v-if="activeTab==='login'" class="space-y-4">
        <input v-model="email" type="email" placeholder="Email"
          class="w-full px-4 py-2 rounded-xl bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-cyan-500" />
        <input v-model="password" type="password" placeholder="Password"
          class="w-full px-4 py-2 rounded-xl bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-cyan-500" />

        <button @click="login"
          class="w-full bg-cyan-600 text-black px-4 py-3 rounded-xl hover:bg-cyan-500 transition font-semibold">
          Login
        </button>

        <p class="text-sm text-center text-cyan-400 cursor-pointer hover:underline" @click="activeTab='reset'">
          Forgot password?
        </p>

        <p v-if="error" class="text-red-500 text-sm text-center">{{ error }}</p>
      </div>

      <!-- Register Form -->
      <div v-if="activeTab==='register'" class="space-y-4">
        <input v-model="regEmail" type="email" placeholder="Email"
          class="w-full px-4 py-2 rounded-xl bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-cyan-500" />
        <input v-model="regPassword" type="password" placeholder="Password"
          class="w-full px-4 py-2 rounded-xl bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-cyan-500" />
        <input v-model="regPasswordConfirm" type="password" placeholder="Confirm Password"
          class="w-full px-4 py-2 rounded-xl bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-cyan-500" />

        <button @click="register"
          class="w-full bg-green-600 text-black px-4 py-3 rounded-xl hover:bg-green-500 transition font-semibold">
          Create Account
        </button>

        <p v-if="regError" class="text-red-500 text-sm text-center">{{ regError }}</p>
      </div>

      <!-- Reset Password Form -->
      <div v-if="activeTab==='reset'" class="space-y-4">
        <input v-model="resetEmail" type="email" placeholder="Enter your registered Email"
          class="w-full px-4 py-2 rounded-xl bg-gray-800 text-white focus:outline-none focus:ring-2 focus:ring-cyan-500" />

        <button @click="resetPassword"
          class="w-full bg-yellow-600 text-black px-4 py-3 rounded-xl hover:bg-yellow-500 transition font-semibold">
          Reset Password
        </button>

        <p v-if="resetMessage" class="text-green-400 text-sm text-center">{{ resetMessage }}</p>
      </div>

    </div>

  </div>
</template>
