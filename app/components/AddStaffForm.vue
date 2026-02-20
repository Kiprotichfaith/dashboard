<template>
  <div class="bg-slate-900 border border-slate-800 p-6 rounded-xl my-8 shadow-xl">
    <div class="flex items-center justify-between mb-6">
      <h2 class="font-bold text-cyan-400 text-lg flex items-center gap-2">
        <span class="w-2 h-6 bg-cyan-500 rounded-full"></span>
        {{ isEditing ? 'Edit Staff Member' : 'Register New Staff' }}
      </h2>
      <button 
        v-if="isEditing" 
        @click="cancelEdit"
        class="text-xs text-slate-400 hover:text-white transition"
      >
        Cancel Edit
      </button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
      <div class="space-y-1">
        <label class="text-[10px] uppercase tracking-wider text-slate-500 font-bold ml-1">Surname</label>
        <input
          v-model="form.surname"
          placeholder="e.g. Kiptoo"
          class="bg-slate-800/50 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 w-full transition-all"
        />
      </div>

      <div class="space-y-1">
        <label class="text-[10px] uppercase tracking-wider text-slate-500 font-bold ml-1">First Name</label>
        <input
          v-model="form.firstname"
          placeholder="e.g. Faith"
          class="bg-slate-800/50 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 w-full transition-all"
        />
      </div>

      <div class="space-y-1">
        <label class="text-[10px] uppercase tracking-wider text-slate-500 font-bold ml-1">PF Number</label>
        <input
          v-model="form.pf"
          placeholder="Unique ID"
          :disabled="isEditing"
          class="bg-slate-800/50 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 w-full disabled:opacity-30 disabled:cursor-not-allowed transition-all"
        />
      </div>

      <div class="space-y-1">
        <label class="text-[10px] uppercase tracking-wider text-slate-500 font-bold ml-1">Email Address</label>
        <input
          v-model="form.email"
          type="email"
          placeholder="staff@kvda.go.ke"
          class="bg-slate-800/50 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 w-full transition-all"
        />
      </div>

      <div class="space-y-1">
        <label class="text-[10px] uppercase tracking-wider text-slate-500 font-bold ml-1">Workstation</label>
        <input
          v-model="form.workstation"
          placeholder="e.g. Eldoret HQ"
          class="bg-slate-800/50 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 w-full transition-all"
        />
      </div>

      <div class="space-y-1">
        <label class="text-[10px] uppercase tracking-wider text-slate-500 font-bold ml-1">Employment Status</label>
        <select
          v-model="form.status"
          class="bg-slate-800/50 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 w-full transition-all appearance-none"
        >
          <option>Active</option>
          <option>Inactive</option>
          <option>Retired</option>
          <option>Exited</option>
        </select>
      </div>
    </div>

    <div class="flex justify-end pt-4">
      <button
        @click="submitForm"
        class="bg-cyan-600 text-black font-bold px-8 py-2.5 rounded-lg hover:bg-cyan-500 hover:shadow-[0_0_15px_rgba(6,182,212,0.4)] transition-all flex items-center gap-2"
      >
        <span v-if="!isEditing">Add Staff Member</span>
        <span v-else>Save Changes</span>
      </button>
    </div>
  </div>
</template>

<script setup>
import { reactive, watch, computed } from 'vue'

const props = defineProps({ editData: Object })
const emit = defineEmits(['add', 'update'])

const form = reactive({
  surname: '',
  firstname: '',
  pf: '',
  email: '',
  workstation: '',
  department: 'ICT', // Defaulted to ICT based on your dashboard title
  status: 'Active'
})

const isEditing = computed(() => !!props.editData)

watch(
  () => props.editData,
  (newVal) => {
    if (newVal) {
      Object.assign(form, newVal)
    } else {
      resetForm()
    }
  },
  { immediate: true }
)

function resetForm() {
  Object.assign(form, {
    surname: '',
    firstname: '',
    pf: '',
    email: '',
    workstation: '',
    department: 'ICT',
    status: 'Active'
  })
}

function cancelEdit() {
  // We emit null to tell the parent to clear the editData prop
  emit('update', null) 
  resetForm()
}

function submitForm() {
  // Basic Validation
  if (!form.surname || !form.pf || !form.email || !form.department) {
    alert('Surname, PF Number, department, and Email are required.')
    return
  }

  if (isEditing.value) {
    emit('update', { ...form })
  } else {
    emit('add', { ...form })
  }
  resetForm()
}
</script>