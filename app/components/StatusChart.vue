<template>
  <div class="w-full">
    <h2 class="text-xs font-bold text-slate-500 uppercase tracking-widest mb-4 text-center">
      Staff Status Distribution
    </h2>

    <div class="h-[220px] w-full flex items-center justify-center">
      <Bar :data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { Bar } from 'vue-chartjs' // Changed from doughnut
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,      // Required for Bar charts
  CategoryScale,   // Required for X-axis labels
  LinearScale      // Required for Y-axis numbers
} from 'chart.js'

// Register the new components needed for a Bar chart
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const props = defineProps({
  staff: {
    type: Array,
    required: true,
    default: () => []
  }
})

// Calculations remain the same logic-wise
const activeCount = computed(() => props.staff.filter(s => s.status === 'Active').length)
const inactiveCount = computed(() => props.staff.filter(s => s.status === 'Inactive').length)
const retiredCount = computed(() => props.staff.filter(s => s.status === 'Retired').length)
const exitedCount = computed(() => props.staff.filter(s => s.status === 'Exited').length)

const chartData = computed(() => ({
  labels: ['Active', 'Inactive', 'Retired', 'Exited'],
  datasets: [
    {
      label: 'Staff Count', 
      data: [
        activeCount.value,
        inactiveCount.value,
        retiredCount.value,
        exitedCount.value
      ],
      backgroundColor: [
        '#22c55e', // green-500
        '#94a3b8', // slate-400
        '#3b82f6', // blue-500
        '#ef4444'  // red-500
      ],
      borderRadius: 8, // Gives the bars a sleek, rounded look
      borderWidth: 0,
    }
  ]
}))

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false // Bar charts usually don't need legends if colors are distinct
    },
    tooltip: {
      backgroundColor: '#0f172a',
      titleColor: '#22d3ee',
      bodyColor: '#fff',
      padding: 12,
      cornerRadius: 8,
    }
  },
  scales: {
    y: {
      beginAtZero: true,
      grid: {
        color: 'rgba(148, 163, 184, 0.1)', // Subtle slate grid lines
        drawBorder: false
      },
      ticks: {
        color: '#94a3b8',
        stepSize: 1, // Since we are counting people, we don't want 1.5 people
        font: {
          family: 'Inter, sans-serif'
        }
      }
    },
    x: {
      grid: {
        display: false // Keeps the background clean
      },
      ticks: {
        color: '#94a3b8',
        font: {
          family: 'Inter, sans-serif',
          weight: 'bold'
        }
      }
    }
  }
}
</script>