<template>
  <div
    class="rounded-2xl border border-gray-200 bg-gray-100 dark:border-gray-800 dark:bg-white/[0.03]"
  >
    <div
      class="px-5 pt-5 bg-white shadow-default rounded-2xl pb-11 dark:bg-gray-900 sm:px-6 sm:pt-6"
    >
      <div class="flex justify-between">
        <div>
          <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">Performance Target</h3>
          <p class="mt-1 text-gray-500 text-theme-sm dark:text-gray-400">
            Monthly operational efficiency metrics
          </p>
        </div>
        <div>
          <DropdownMenu :menu-items="menuItems">
            <template #icon>
              <svg
                width="24"
                height="24"
                viewBox="0 0 24 24"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  fillRule="evenodd"
                  clipRule="evenodd"
                  d="M10.2441 6C10.2441 5.0335 11.0276 4.25 11.9941 4.25H12.0041C12.9706 4.25 13.7541 5.0335 13.7541 6C13.7541 6.9665 12.9706 7.75 12.0041 7.75H11.9941C11.0276 7.75 10.2441 6.9665 10.2441 6ZM10.2441 18C10.2441 17.0335 11.0276 16.25 11.9941 16.25H12.0041C12.9706 16.25 13.7541 17.0335 13.7541 18C13.7541 18.9665 12.9706 19.75 12.0041 19.75H11.9941C11.0276 19.75 10.2441 18.9665 10.2441 18ZM11.9941 10.25C11.0276 10.25 10.2441 11.0335 10.2441 12C10.2441 12.9665 11.0276 13.75 11.9941 13.75H12.0041C12.9706 13.75 13.7541 12.9665 13.7541 12C13.7541 11.0335 12.9706 10.25 12.0041 10.25H11.9941Z"
                  fill="currentColor"
                />
              </svg>
            </template>
          </DropdownMenu>
        </div>
      </div>
      <div class="relative max-h-[200px] mt-4">
        <div id="performanceChart" class="h-full">
          <div class="radial-bar-chart">
            <VueApexCharts type="radialBar" height="340" :options="chartOptions" :series="series" />
          </div>
        </div>
        <span
          class="absolute left-1/2 top-[85%] -translate-x-1/2 -translate-y-[85%] rounded-full px-3 py-1 text-xs font-medium"
          :class="performanceIndicator.badgeClass"
        >
          {{ performanceIndicator.text }}
        </span>
      </div>
      <!-- <p class="mx-auto mt-1.5 w-full max-w-[380px] text-center text-sm text-gray-500 sm:text-base">
        Current efficiency is {{ currentEfficiency }}%, {{ performanceMessage }}
      </p> -->
    </div>

    <div class="flex items-center justify-center gap-5 px-6 py-3.5 sm:gap-8 sm:py-5">
      <div class="text-center">
        <p class="mb-1 text-gray-500 text-theme-xs dark:text-gray-400 sm:text-sm">
          Target
        </p>
        <p
          class="flex items-center justify-center gap-1 text-base font-semibold text-gray-800 dark:text-white/90 sm:text-lg"
        >
          {{ performanceTarget }}%
          <svg
            width="16"
            height="16"
            viewBox="0 0 16 16"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              fill-rule="evenodd"
              clip-rule="evenodd"
              d="M7.60141 2.33683C7.73885 2.18084 7.9401 2.08243 8.16435 2.08243C8.16475 2.08243 8.16516 2.08243 8.16556 2.08243C8.35773 2.08219 8.54998 2.15535 8.69664 2.30191L12.6968 6.29924C12.9898 6.59203 12.9899 7.0669 12.6971 7.3599C12.4044 7.6529 11.9295 7.65306 11.6365 7.36027L8.91435 4.64004L8.91435 13.5C8.91435 13.9142 8.57856 14.25 8.16435 14.25C7.75013 14.25 7.41435 13.9142 7.41435 13.5L7.41435 4.64442L4.69679 7.36025C4.4038 7.65305 3.92893 7.6529 3.63613 7.35992C3.34333 7.06693 3.34348 6.59206 3.63646 6.29926L7.60141 2.33683Z"
              fill="#039855"
            />
          </svg>
        </p>
      </div>

      <div class="w-px bg-gray-200 h-7 dark:bg-gray-800"></div>

      <div class="text-center">
        <p class="mb-1 text-gray-500 text-theme-xs dark:text-gray-400 sm:text-sm">
          Throughput
        </p>
        <p
          class="flex items-center justify-center gap-1 text-base font-semibold text-gray-800 dark:text-white/90 sm:text-lg"
        >
          {{ throughput }}
          <svg
            width="16"
            height="16"
            viewBox="0 0 16 16"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              fill-rule="evenodd"
              clip-rule="evenodd"
              d="M7.60141 2.33683C7.73885 2.18084 7.9401 2.08243 8.16435 2.08243C8.16475 2.08243 8.16516 2.08243 8.16556 2.08243C8.35773 2.08219 8.54998 2.15535 8.69664 2.30191L12.6968 6.29924C12.9898 6.59203 12.9899 7.0669 12.6971 7.3599C12.4044 7.6529 11.9295 7.65306 11.6365 7.36027L8.91435 4.64004L8.91435 13.5C8.91435 13.9142 8.57856 14.25 8.16435 14.25C7.75013 14.25 7.41435 13.9142 7.41435 13.5L7.41435 4.64442L4.69679 7.36025C4.4038 7.65305 3.92893 7.6529 3.63613 7.35992C3.34333 7.06693 3.34348 6.59206 3.63646 6.29926L7.60141 2.33683Z"
              fill="#039855"
            />
          </svg>
        </p>
      </div>

      <div class="w-px bg-gray-200 h-7 dark:bg-gray-800"></div>

      <div class="text-center">
        <p class="mb-1 text-gray-500 text-theme-xs dark:text-gray-400 sm:text-sm">
          Accuracy
        </p>
        <p
          class="flex items-center justify-center gap-1 text-base font-semibold text-gray-800 dark:text-white/90 sm:text-lg"
        >
          {{ accuracy }}%
          <svg
            width="16"
            height="16"
            viewBox="0 0 16 16"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
            :class="accuracyTrend === 'up' ? '' : 'transform rotate-180'"
          >
            <path
              fill-rule="evenodd"
              clip-rule="evenodd"
              d="M7.60141 2.33683C7.73885 2.18084 7.9401 2.08243 8.16435 2.08243C8.16475 2.08243 8.16516 2.08243 8.16556 2.08243C8.35773 2.08219 8.54998 2.15535 8.69664 2.30191L12.6968 6.29924C12.9898 6.59203 12.9899 7.0669 12.6971 7.3599C12.4044 7.6529 11.9295 7.65306 11.6365 7.36027L8.91435 4.64004L8.91435 13.5C8.91435 13.9142 8.57856 14.25 8.16435 14.25C7.75013 14.25 7.41435 13.9142 7.41435 13.5L7.41435 4.64442L4.69679 7.36025C4.4038 7.65305 3.92893 7.6529 3.63613 7.35992C3.34333 7.06693 3.34348 6.59206 3.63646 6.29926L7.60141 2.33683Z"
              :fill="accuracyTrend === 'up' ? '#039855' : '#D92D20'"
            />
          </svg>
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import DropdownMenu from '../common/DropdownMenu.vue'
import VueApexCharts from 'vue3-apexcharts'

const menuItems = [
  { label: 'View Details', onClick: () => console.log('View Details clicked') },
  { label: 'Export Report', onClick: () => console.log('Export Report clicked') },
  { label: 'Set Target', onClick: () => console.log('Set Target clicked') },
]

const props = defineProps({
  value: {
    type: Number,
    default: 88.7,
  },
})

// Performance metrics
const currentEfficiency = ref(88.7)
const performanceTarget = ref(90)
const throughput = ref('2.4K/h')
const accuracy = ref(97.2)
const accuracyTrend = ref('down') // 'up' or 'down'

const series = computed(() => [currentEfficiency.value])

const performanceIndicator = computed(() => {
  const diff = currentEfficiency.value - performanceTarget.value
  if (diff >= 0) {
    return {
      text: `+${diff.toFixed(1)}%`,
      badgeClass: 'bg-green-50 text-green-600 dark:bg-green-500/15 dark:text-green-500'
    }
  } else {
    return {
      text: `${diff.toFixed(1)}%`,
      badgeClass: 'bg-red-50 text-red-600 dark:bg-red-500/15 dark:text-red-500'
    }
  }
})

const performanceMessage = computed(() => {
  const diff = currentEfficiency.value - performanceTarget.value
  if (diff >= 2) {
    return 'exceeding expectations! Great performance.'
  } else if (diff >= 0) {
    return 'meeting target goals. Keep up the good work.'
  } else if (diff >= -2) {
    return 'slightly below target. Minor improvements needed.'
  } else {
    return 'below target. Attention required for optimization.'
  }
})

const chartOptions = {
  colors: ['#3B82F6'],
  chart: {
    fontFamily: 'Outfit, sans-serif',
    sparkline: {
      enabled: true,
    },
  },
  plotOptions: {
    radialBar: {
      startAngle: -90,
      endAngle: 90,
      hollow: {
        size: '75%',
      },
      track: {
        background: '#E5E7EB',
        strokeWidth: '100%',
        margin: 8,
      },
      dataLabels: {
        name: {
          show: false,
        },
        value: {
          fontSize: '32px',
          fontWeight: '700',
          offsetY: 50,
          color: '#1F2937',
          formatter: function (val) {
            return val.toFixed(1) + '%'
          },
        },
      },
    },
  },
  fill: {
    type: 'gradient',
    gradient: {
      shade: 'light',
      type: 'horizontal',
      shadeIntensity: 0.5,
      gradientToColors: ['#1D4ED8'],
      inverseColors: true,
      opacityFrom: 1,
      opacityTo: 1,
      stops: [0, 100],
    },
  },
  stroke: {
    lineCap: 'round',
  },
  labels: ['Performance'],
}
</script>

<style scoped>
.radial-bar-chart {
  width: 100%;
  max-width: 340px;
  margin: 0 auto;
}
</style>