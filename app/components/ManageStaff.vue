<template>
  <div class="fixed inset-0 bg-slate-950/80 backdrop-blur-sm flex items-center justify-center z-[100] p-4">
    <div class="bg-slate-900 border border-slate-800 p-8 rounded-2xl w-full max-w-md shadow-2xl space-y-6 relative">
      
      <button 
        @click="$emit('close')" 
        class="absolute top-4 right-4 text-slate-500 hover:text-white transition"
      >
        <span class="text-xl">✕</span>
      </button>

      <div class="text-center">
        <div class="w-16 h-16 bg-cyan-500/10 rounded-full flex items-center justify-center mx-auto mb-4">
          <span class="text-2xl text-cyan-400">🔐</span>
        </div>
        <h2 class="text-xl font-bold text-white">Security Management</h2>
        <p class="text-sm text-slate-400 mt-1">
          Updating credentials for: <br/>
          <span class="text-cyan-400 font-medium">{{ staff.firstname }} {{ staff.surname }}</span>
        </p>
      </div>

      <div class="space-y-4">
        <div class="space-y-2">
          <label class="text-xs font-bold text-slate-500 uppercase tracking-widest ml-1">New System Password</label>
          <div class="relative">
            <input
              v-model="password"
              :type="showPassword ? 'text' : 'password'"
              placeholder="••••••••"
              class="w-full bg-slate-800 text-white px-4 py-3 rounded-xl border border-slate-700 focus:ring-2 focus:ring-cyan-500 focus:border-transparent outline-none transition-all pr-24"
            />
            <div class="absolute right-2 top-1/2 -translate-y-1/2 flex gap-1">
              <button 
                @click="showPassword = !showPassword"
                class="p-2 text-xs text-slate-400 hover:text-cyan-400"
                title="Toggle Visibility"
              >
                {{ showPassword ? '🙈' : '👁️' }}
              </button>
              <button 
                @click="copyPassword"
                class="p-2 text-xs text-slate-400 hover:text-cyan-400"
                title="Copy Password"
              >
                📋
              </button>
            </div>
          </div>
          
          <div class="h-1 w-full bg-slate-800 rounded-full overflow-hidden">
            <div 
              class="h-full transition-all duration-500" 
              :class="strengthColor"
              :style="{ width: password.length > 0 ? (password.length * 10) + '%' : '0%', maxWidth: '100%' }"
            ></div>
          </div>
        </div>

        <div class="grid grid-cols-2 gap-3">
          <button
            @click="generatePassword"
            class="bg-slate-800 text-slate-300 px-4 py-3 rounded-xl hover:bg-slate-700 border border-slate-700 transition font-medium text-sm"
          >
            Auto-Generate
          </button>

          <button
            @click="submit"
            :disabled="!password"
            class="bg-cyan-600 disabled:opacity-50 disabled:cursor-not-allowed text-black px-4 py-3 rounded-xl hover:bg-cyan-500 transition font-bold text-sm shadow-lg shadow-cyan-900/20"
          >
            Update Access
          </button>
        </div>
      </div>

      <p class="text-[10px] text-slate-500 text-center uppercase tracking-tighter">
        Action will be logged under administrator ICT-SEC-AUDIT
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  staff: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update', 'close'])

const password = ref('')
const showPassword = ref(false)

const strengthColor = computed(() => {
  if (password.value.length < 5) return 'bg-red-500'
  if (password.value.length < 8) return 'bg-yellow-500'
  return 'bg-green-500'
})

function generatePassword() {
  const chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789!@#$%^&*"
  let newPass = ""
  for (let i = 0; i < 12; i++) {
    newPass += chars.charAt(Math.floor(Math.random() * chars.length))
  }
  password.value = newPass
  showPassword.value = true // Show it so the admin can read it
}

function copyPassword() {
  if (!password.value) return
  navigator.clipboard.writeText(password.value)
  alert('Password copied to clipboard!')
}

function submit() {
  if (!password.value) return
  emit('update', {
    pf: props.staff.pf,
    password: password.value
  })
  password.value = ''
  emit('close')
}
</script>