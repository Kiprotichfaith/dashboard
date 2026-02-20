<template>
  <nav class="fixed top-0 left-0 right-0 z-[100] bg-slate-950/80 backdrop-blur-md border-b border-slate-800">
    <div class="max-w-7xl mx-auto px-6 h-16 flex items-center justify-between">
      
      <div class="flex items-center gap-3 shrink-0">
        <img src="/images/kvdalogo.png" alt="KVDA Logo" class="h-10 w-auto object-contain" />
        <span class="text-lg font-black italic tracking-tighter uppercase text-white border-l border-slate-800 pl-3">Personnel Portal</span>
      </div>
    
      <div class="flex items-center gap-4">
        <button
          v-if="!loggedIn"
          @click="showLogin = true; activeTab = 'login'"
          class="bg-cyan-600 px-6 py-2 rounded-lg text-black font-bold hover:bg-cyan-500 transition-all shadow-lg shadow-cyan-900/20 active:scale-95 text-sm"
        >
          Access Portal
        </button>
      
        <div v-else class="flex items-center gap-4">
           <div class="hidden md:flex items-center gap-3 border-r border-slate-800 pr-4">
              <span class="text-[10px] bg-cyan-500/10 text-cyan-500 px-2 py-1 rounded font-black uppercase tracking-widest">Admin</span>
              <span class="text-xs text-slate-400 font-medium italic">admin@kvda.go.ke</span>
           </div>
           
           <button
            @click="logout"
            class="bg-slate-800 text-red-400 border border-red-900/30 px-4 py-2 rounded-lg hover:bg-red-900/40 transition-all font-semibold text-sm flex items-center gap-2"
          >
            Sign Out
          </button>
        </div>
      </div>
    </div>

    <Teleport to="body">
      <div v-if="showLogin && !loggedIn" 
           class="fixed inset-0 z-[9999] flex items-center justify-center p-6">
        
        <div class="absolute inset-0 bg-slate-950/98 backdrop-blur-xl" @click="showLogin = false"></div>
        
        <div class="bg-slate-900 border border-slate-800 p-8 rounded-3xl w-full max-w-md space-y-6 relative shadow-[0_0_50px_rgba(0,0,0,0.8)] animate-in zoom-in duration-300">
          
          <button @click="showLogin = false" class="absolute top-4 right-4 text-slate-500 hover:text-red-500 transition-colors text-xl">✕</button>

          <div class="text-center">
            <h2 class="text-white font-black italic tracking-tighter text-2xl uppercase">System Authorization</h2>
            <p class="text-[10px] text-slate-500 font-bold uppercase tracking-widest mt-1">Identity Verification Required</p>
          </div>

          <div class="flex justify-center border-b border-slate-800">
            <button @click="activeTab = 'login'" :class="['px-6 py-2 text-sm font-bold uppercase transition-all', activeTab === 'login' ? 'text-cyan-400 border-b-2 border-cyan-400' : 'text-slate-500']">Login</button>
            <button @click="activeTab = 'register'" :class="['px-6 py-2 text-sm font-bold uppercase transition-all', activeTab === 'register' ? 'text-cyan-400 border-b-2 border-cyan-400' : 'text-slate-500']">Register</button>
          </div>

          <div v-if="activeTab === 'login'" class="space-y-4 pt-2">
            <div class="space-y-1">
              <label class="text-[10px] text-slate-500 uppercase font-bold ml-1">Admin Email</label>
              <input v-model="email" type="email" placeholder="admin@kvda.go.ke" class="w-full px-4 py-3 rounded-xl bg-slate-800 text-white border border-slate-700 focus:ring-2 focus:ring-cyan-500 outline-none transition-all" />
            </div>
            <div class="space-y-1">
              <label class="text-[10px] text-slate-500 uppercase font-bold ml-1">Password</label>
              <input v-model="password" type="password" placeholder="••••••••" class="w-full px-4 py-3 rounded-xl bg-slate-800 text-white border border-slate-700 focus:ring-2 focus:ring-cyan-500 outline-none transition-all" />
            </div>
            <button @click="login" class="w-full bg-cyan-600 text-black font-bold py-4 rounded-xl hover:bg-cyan-500 transition shadow-lg shadow-cyan-900/20 active:scale-95 uppercase tracking-widest text-xs">
              Authorize Access
            </button>
            <p v-if="error" class="text-red-500 text-xs text-center font-bold bg-red-500/10 py-2 rounded-lg border border-red-500/20">{{ error }}</p>
          </div>

          <div v-else class="text-center py-8 text-slate-500 italic text-sm">
            Portal registration is restricted. Contact ICT Head.
          </div>
        </div>
      </div>
    </Teleport>
  </nav>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const showLogin = ref(false)
const activeTab = ref('login')
const loggedIn = ref(false)
const email = ref('')
const password = ref('')
const error = ref('')

onMounted(() => {
  if (process.client && localStorage.getItem('loggedIn') === 'true') {
    loggedIn.value = true
  }
})

function login() {
  if (email.value === 'admin@kvda.go.ke' && password.value === 'admin123') {
    loggedIn.value = true
    if (process.client) localStorage.setItem('loggedIn', 'true')
    showLogin.value = false
    window.location.reload() 
  } else {
    error.value = 'ACCESS DENIED: Invalid Administrative Credentials'
  }
}

function logout() {
  loggedIn.value = false
  if (process.client) localStorage.removeItem('loggedIn')
  window.location.reload()
}
</script>