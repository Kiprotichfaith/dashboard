<template>
  <footer class="bg-slate-900 border-t border-slate-800 mt-10">
    <div class="max-w-7xl mx-auto px-6 py-4 flex flex-col md:flex-row items-center justify-between gap-2">

      <p class="text-sm text-slate-400">
        © {{ year }} Corporate ICT Department. All rights reserved.
      </p>

    
    </div>
  </footer>
</template>

<script setup>
const year = new Date().getFullYear()
</script>
