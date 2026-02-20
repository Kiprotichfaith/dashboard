<template>
  <div class="bg-slate-900/50 border border-slate-800 p-4 rounded-xl flex flex-wrap gap-4 items-center shadow-inner">
    
    <div class="relative flex-1 min-w-[280px]">
      <span class="absolute left-3 top-1/2 -translate-y-1/2 text-slate-500">
        🔍
      </span>
      <input
        :value="search"
        @input="$emit('update:search', $event.target.value)"
        placeholder="Search by name, email or PF..."
        class="w-full bg-slate-800 text-white placeholder-slate-500 border border-slate-700 rounded-lg pl-10 pr-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 transition-all"
      />
    </div>

    <div class="flex items-center gap-2">
      <label class="text-xs font-bold text-slate-500 uppercase tracking-tighter">Status:</label>
      <select
        :value="status"
        @change="$emit('update:status', $event.target.value)"
        class="bg-slate-800 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 appearance-none cursor-pointer min-w-[120px]"
      >
        <option value="">All Staff</option>
        <option>Active</option>
        <option>Inactive</option>
        <option>Retired</option>
        <option>Exited</option>
      </select>
    </div>

    <div class="flex items-center gap-2">
      <label class="text-xs font-bold text-slate-500 uppercase tracking-tighter">View:</label>
      <select
        :value="perPage"
        @change="$emit('update:perPage', Number($event.target.value))"
        class="bg-slate-800 text-white border border-slate-700 rounded-lg px-4 py-2.5 focus:outline-none focus:ring-2 focus:ring-cyan-500/50 appearance-none cursor-pointer"
      >
        <option v-for="n in [10, 20, 30, 50, 100]" :key="n" :value="n">
          {{ n }} rows
        </option>
      </select>
    </div>

    <button 
      v-if="search || status"
      @click="$emit('update:search', ''); $emit('update:status', '')"
      class="text-xs text-cyan-500 hover:text-cyan-400 font-medium transition px-2"
    >
      Clear Filters
    </button>
  </div>
</template>

<script setup>
defineProps({
  search: String,
  status: String,
  perPage: Number
})

defineEmits(['update:search', 'update:status', 'update:perPage'])
</script>