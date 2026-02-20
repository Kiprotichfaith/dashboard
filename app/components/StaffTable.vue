<template>
  <div class="w-full bg-slate-900 border border-slate-800 rounded-xl overflow-hidden shadow-2xl">
    <div class="overflow-x-auto">
      <table class="min-w-full divide-y divide-slate-800">
        <thead class="bg-slate-800/50">
          <tr>
            <th class="p-6 text-left w-12">
              <input 
                type="checkbox" 
                @change="$emit('toggle-all')" 
                :checked="isAllSelected"
                class="accent-cyan-500 rounded border-slate-700 bg-slate-950"
              />
            </th>
            <th class="px-6 py-4 text-left text-xs font-bold text-cyan-400 uppercase tracking-widest">#</th>
            <th scope="col" class="px-6 py-4 text-left text-xs font-bold text-cyan-400 uppercase tracking-widest">
              surname
            </th>
            <th scope="col" class="px-6 py-4 text-left text-xs font-bold text-cyan-400 uppercase tracking-widest">
              firstname
            </th>
            <th scope="col" class="px-6 py-4 text-left text-xs font-bold text-cyan-400 uppercase tracking-widest">
              PF Number
            </th>
            <th scope="col" class="px-6 py-4 text-left text-xs font-bold text-cyan-400 uppercase tracking-widest">
              Department
            </th>
            <th scope="col" class="px-6 py-4 text-left text-xs font-bold text-cyan-400 uppercase tracking-widest">
              Workstation
            </th>
            <th scope="col" class="px-6 py-4 text-left text-xs font-bold text-cyan-400 uppercase tracking-widest">
              Status
            </th>
            <th scope="col" class="px-6 py-4 text-right text-xs font-bold text-cyan-400 uppercase tracking-widest">
              Operations
            </th>
          </tr>
        </thead>
        <tbody class="divide-y divide-slate-800 bg-slate-900">
          <tr 
            v-for="(s, index) in staff" 
            :key="s.pf" 
            class="hover:bg-cyan-500/[0.03] transition-colors group"
            :class="{'bg-cyan-500/[0.05]': selectedIds.includes(s.pf)}"
          >
            <td class="p-6">
              <input 
                type="checkbox" 
                :checked="selectedIds.includes(s.pf)"
                @change="$emit('toggle-one', s.pf)"
                class="accent-cyan-500 rounded border-slate-700 bg-slate-950"
              />
            </td>

            <td class="px-6 py-4 whitespace-nowrap text-sm font-mono text-slate-500">
              {{ index + 1 }}
            </td>

            <td class="px-6 py-4 whitespace-nowrap">
              <div class="flex items-center">
                <div class="h-9 w-9 rounded-full bg-slate-800 border border-slate-700 flex items-center justify-center text-cyan-400 font-bold text-xs shadow-inner">
                  {{ s.firstname[0] }}{{ s.surname[0] }}
                </div>
                <div class="ml-4">
                  <div class="text-sm font-semibold text-slate-100">{{ s.surname }}, {{ s.firstname }}</div>
                  <div class="text-xs text-slate-500 font-mono italic">ID: #{{ s.pf.slice(-3) }}</div>
                </div>
              </div>
            </td>

            <td class="px-6 py-4 whitespace-nowrap text-sm text-slate-300">
              {{ s.firstname }}
            </td>

            <td class="px-6 py-4 whitespace-nowrap">
              <span class="px-2 py-1 bg-slate-800 text-slate-300 rounded border border-slate-700 font-mono text-xs">
                {{ s.pf }}
              </span>
            </td>

            <td class="px-6 py-4 whitespace-nowrap">
              <div class="text-sm text-slate-300">{{ s.email }}</div>
              <div class="text-[10px] text-cyan-600 uppercase font-bold tracking-tighter">{{ s.department }}</div>
            </td>

            <td class="px-6 py-4 whitespace-nowrap">
              <div class="flex items-center text-sm text-slate-400">
                <span class="mr-1.5 text-xs">📍</span>
                {{ s.workstation }}
              </div>
            </td>

            <td class="px-6 py-4 whitespace-nowrap">
              <span 
                class="px-3 py-1 inline-flex text-[10px] leading-5 font-bold rounded-full border"
                :class="statusClasses(s.status)"
              >
                {{ s.status }}
              </span>
            </td>

            <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
              <div class="flex justify-end gap-3 opacity-60 group-hover:opacity-100 transition-opacity">
                <button 
                  @click="$emit('edit', s)" 
                  class="text-yellow-500 hover:text-yellow-400 transition"
                  title="Edit Record"
                >
                  Edit
                </button>
                <button 
                  @click="$emit('manage', s)" 
                  class="text-blue-400 hover:text-blue-300 transition"
                  title="Security/Password"
                >
                  Manage
                </button>
                <button 
                  @click="$emit('delete', s.pf)" 
                  class="text-red-500 hover:text-red-400 transition"
                  title="Delete Record"
                >
                  Delete
                </button>
              </div>
            </td>
          </tr>

          <tr v-if="staff.length === 0">
            <td colspan="9" class="px-6 py-12 text-center">
              <div class="text-slate-500 italic">No staff members found in the current directory.</div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
defineProps({
  staff: {
    type: Array,
    required: true
  },
  // Added to track selections from index
  selectedIds: {
    type: Array,
    default: () => []
  },
  isAllSelected: {
    type: Boolean,
    default: false
  }
})

// Added selection emits
defineEmits(['edit', 'manage', 'delete', 'toggle-all', 'toggle-one'])

function statusClasses(status) {
  switch (status) {
    case 'Active':
      return 'bg-green-500/10 text-green-400 border-green-500/20'
    case 'Inactive':
      return 'bg-slate-700/30 text-slate-400 border-slate-600/30'
    case 'Retired':
      return 'bg-blue-500/10 text-blue-400 border-blue-500/20'
    case 'Exited':
      return 'bg-red-500/10 text-red-400 border-red-500/20'
    default:
      return 'bg-slate-800 text-slate-300 border-slate-700'
  }
}
</script>npm