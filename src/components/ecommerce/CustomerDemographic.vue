<template>
  <div
    class="rounded-2xl border border-gray-200 bg-white px-5 pb-5 pt-5 dark:border-gray-800 dark:bg-white/[0.03] sm:px-6 sm:pt-6"
  >
    <div class="flex flex-col gap-5 mb-6 sm:flex-row sm:justify-between">
      <div class="w-full">
        <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">Warehouse Analytics</h3>
        <p class="mt-1 text-gray-500 text-theme-sm dark:text-gray-400">
          Performance metrics and operational efficiency
        </p>
      </div>

      <div class="relative">
        <div class="inline-flex items-center gap-0.5 rounded-lg bg-gray-100 p-0.5 dark:bg-gray-900">
          <button
            v-for="option in options"
            :key="option.value"
            @click="selected = option.value; updateChartData()"
            :class="[
              selected === option.value
                ? 'shadow-theme-xs text-gray-900 dark:text-white bg-white dark:bg-gray-800'
                : 'text-gray-500 dark:text-gray-400',
              'px-3 py-2 font-medium rounded-md text-theme-sm hover:text-gray-900 hover:shadow-theme-xs dark:hover:bg-gray-800 dark:hover:text-white transition-all duration-200',
            ]"
          >
            {{ option.label }}
          </button>
        </div>
      </div>
    </div>

    <!-- Key Performance Indicators -->
    <div class="grid grid-cols-1 sm:grid-cols-4 gap-4 mb-6">
      <div class="p-4 rounded-lg bg-blue-50 dark:bg-blue-900/20">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm font-medium text-blue-600 dark:text-blue-400">Throughput</p>
            <p class="text-xl font-bold text-blue-700 dark:text-blue-300">{{ currentThroughput }}</p>
          </div>
          <div class="p-2 bg-blue-100 rounded-lg dark:bg-blue-800/30">
            <svg class="w-4 h-4 text-blue-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M2 10a8 8 0 018-8v8h8a8 8 0 11-16 0z"/>
              <path d="M12 2.252A8.014 8.014 0 0117.748 8H12V2.252z"/>
            </svg>
          </div>
        </div>
        <div class="flex items-center mt-2 text-xs">
          <span class="text-green-600 font-medium">+8.2%</span>
          <span class="ml-1 text-gray-500">vs last period</span>
        </div>
      </div>

      <div class="p-4 rounded-lg bg-green-50 dark:bg-green-900/20">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm font-medium text-green-600 dark:text-green-400">Efficiency</p>
            <p class="text-xl font-bold text-green-700 dark:text-green-300">{{ currentEfficiency }}%</p>
          </div>
          <div class="p-2 bg-green-100 rounded-lg dark:bg-green-800/30">
            <svg class="w-4 h-4 text-green-600" fill="currentColor" viewBox="0 0 20 20">
              <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />
            </svg>
          </div>
        </div>
        <div class="flex items-center mt-2 text-xs">
          <span class="text-green-600 font-medium">+12.1%</span>
          <span class="ml-1 text-gray-500">vs target</span>
        </div>
      </div>

      <div class="p-4 rounded-lg bg-orange-50 dark:bg-orange-900/20">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm font-medium text-orange-600 dark:text-orange-400">Accuracy</p>
            <p class="text-xl font-bold text-orange-700 dark:text-orange-300">{{ currentAccuracy }}%</p>
          </div>
          <div class="p-2 bg-orange-100 rounded-lg dark:bg-orange-800/30">
            <svg class="w-4 h-4 text-orange-600" fill="currentColor" viewBox="0 0 20 20">
              <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-12a1 1 0 10-2 0v4a1 1 0 00.293.707l2.828 2.829a1 1 0 101.415-1.415L11 9.586V6z" clipRule="evenodd" />
            </svg>
          </div>
        </div>
        <div class="flex items-center mt-2 text-xs">
          <span class="text-red-600 font-medium">-2.3%</span>
          <span class="ml-1 text-gray-500">needs attention</span>
        </div>
      </div>

      <div class="p-4 rounded-lg bg-purple-50 dark:bg-purple-900/20">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm font-medium text-purple-600 dark:text-purple-400">Cost per Unit</p>
            <p class="text-xl font-bold text-purple-700 dark:text-purple-300">${{ currentCostPerUnit }}</p>
          </div>
          <div class="p-2 bg-purple-100 rounded-lg dark:bg-purple-800/30">
            <svg class="w-4 h-4 text-purple-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M8.433 7.418c.155-.103.346-.196.567-.267v1.698a2.305 2.305 0 01-.567-.267C8.07 8.34 8 8.114 8 8c0-.114.07-.34.433-.582zM11 12.849v-1.698c.22.071.412.164.567.267.364.243.433.468.433.582 0 .114-.07.34-.433.582a2.305 2.305 0 01-.567.267z"/>
              <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-13a1 1 0 10-2 0v.092a4.535 4.535 0 00-1.676.662C6.602 6.234 6 7.009 6 8c0 .99.602 1.765 1.324 2.246.48.32 1.054.545 1.676.662v1.941c-.391-.127-.68-.317-.843-.504a1 1 0 10-1.51 1.31c.562.649 1.413 1.076 2.353 1.253V15a1 1 0 102 0v-.092a4.535 4.535 0 001.676-.662C13.398 13.766 14 12.991 14 12c0-.99-.602-1.765-1.324-2.246A4.535 4.535 0 0011 9.092V7.151c.391.127.68.317.843.504a1 1 0 101.511-1.31c-.563-.649-1.413-1.076-2.354-1.253V5z" clipRule="evenodd" />
            </svg>
          </div>
        </div>
        <div class="flex items-center mt-2 text-xs">
          <span class="text-green-600 font-medium">-5.4%</span>
          <span class="ml-1 text-gray-500">cost reduction</span>
        </div>
      </div>
    </div>

    <div class="max-w-full overflow-x-auto custom-scrollbar">
      <div id="warehouseChart" class="-ml-4 min-w-[1000px] xl:min-w-full pl-2">
        <VueApexCharts type="area" height="320" :options="chartOptions" :series="series" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import VueApexCharts from 'vue3-apexcharts'

const options = [
  { value: 'weekly', label: 'Weekly' },
  { value: 'monthly', label: 'Monthly' },
  { value: 'quarterly', label: 'Quarterly' },
]

const selected = ref('weekly')

// KPI Values
const currentThroughput = ref('2,847')
const currentEfficiency = ref('94.2')
const currentAccuracy = ref('98.7')
const currentCostPerUnit = ref('3.24')

const chartData = reactive({
  weekly: {
    inbound: [180, 220, 170, 160, 175, 195, 210],
    outbound: [140, 190, 150, 180, 165, 170, 190],
    processing: [85, 95, 80, 90, 88, 92, 98],
  },
  monthly: {
    inbound: [2800, 3200, 2900, 3100, 2700, 3400, 3600, 3200, 3500, 3300, 3700, 3900],
    outbound: [2600, 3000, 2750, 2900, 2500, 3200, 3400, 3000, 3300, 3100, 3500, 3700],
    processing: [1200, 1400, 1300, 1350, 1150, 1500, 1600, 1450, 1550, 1480, 1650, 1750],
  },
  quarterly: {
    inbound: [9500, 10200, 9800, 10500],
    outbound: [8900, 9600, 9200, 9800],
    processing: [4200, 4600, 4400, 4800],
  }
})

const series = ref([
  {
    name: 'Inbound Operations',
    data: chartData.weekly.inbound,
  },
  {
    name: 'Outbound Operations', 
    data: chartData.weekly.outbound,
  },
  {
    name: 'Processing',
    data: chartData.weekly.processing,
  },
])

const chartOptions = ref({
  legend: {
    show: true,
    position: 'top',
    horizontalAlign: 'left',
    fontFamily: 'Outfit',
    markers: {
      radius: 99,
    },
  },
  colors: ['#3B82F6', '#10B981', '#F59E0B'],
  chart: {
    fontFamily: 'Outfit, sans-serif',
    type: 'area',
    toolbar: {
      show: false,
    },
    animations: {
      enabled: true,
      easing: 'easeinout',
      speed: 800,
    },
  },
  fill: {
    type: 'gradient',
    gradient: {
      enabled: true,
      opacityFrom: 0.6,
      opacityTo: 0.1,
    },
  },
  stroke: {
    curve: 'smooth',
    width: [3, 3, 3],
  },
  markers: {
    size: 0,
    hover: {
      size: 6,
    },
  },
  grid: {
    xaxis: {
      lines: {
        show: false,
      },
    },
    yaxis: {
      lines: {
        show: true,
      },
    },
  },
  dataLabels: {
    enabled: false,
  },
  tooltip: {
    shared: true,
    intersect: false,
    y: {
      formatter: function (val) {
        return val.toLocaleString() + ' units'
      },
    },
  },
  xaxis: {
    type: 'category',
    categories: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
    axisBorder: {
      show: false,
    },
    axisTicks: {
      show: false,
    },
  },
  yaxis: {
    title: {
      text: 'Operations Volume',
      style: {
        fontSize: '12px',
        fontWeight: 500,
      },
    },
    labels: {
      formatter: function (val) {
        return val.toLocaleString()
      },
    },
  },
})

const updateChartData = () => {
  const selectedData = chartData[selected.value]
  let categories = []
  
  switch (selected.value) {
    case 'weekly':
      categories = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
      break
    case 'monthly':
      categories = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
      break
    case 'quarterly':
      categories = ['Q1', 'Q2', 'Q3', 'Q4']
      break
  }
  
  series.value = [
    { name: 'Inbound Operations', data: selectedData.inbound },
    { name: 'Outbound Operations', data: selectedData.outbound },
    { name: 'Processing', data: selectedData.processing },
  ]
  
  chartOptions.value = {
    ...chartOptions.value,
    xaxis: {
      ...chartOptions.value.xaxis,
      categories: categories,
    },
  }
}
</script>