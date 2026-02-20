<template>
  <div class="bg-white shadow rounded p-4 text-center">
    <p class="text-gray-500">{{ title }}</p>
    <p class="text-3xl font-bold text-blue-600">{{ value }}</p>
  </div>
</template>

<script setup>
defineProps({
  title: String,
  value: Number
})
</script>
