<template>
  <div class="grid grid-cols-1 gap-4 sm:grid-cols-2 md:gap-6">
    <!-- Total Inventory -->
    <div class="rounded-2xl border border-gray-200 bg-white p-5 dark:border-gray-800 dark:bg-white/[0.03] md:p-6 hover:shadow-lg transition-all duration-300">
      <div class="flex items-center justify-center w-12 h-12 bg-blue-100 rounded-xl dark:bg-blue-800/20">
        <svg class="fill-blue-600 dark:fill-blue-400" width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"/>
          <polyline points="3.27,6.96 12,12.01 20.73,6.96"/>
          <line x1="12" y1="22.08" x2="12" y2="12"/>
        </svg>
      </div>

      <div class="flex items-end justify-between mt-5">
        <div>
          <span class="text-sm text-gray-500 dark:text-gray-400">Total Inventory</span>
          <h4 class="mt-2 font-bold text-gray-800 text-title-sm dark:text-white/90">{{ formatNumber(totalInventory) }}</h4>
          <div class="flex items-center mt-2 text-sm">
            <span class="text-green-600 font-medium">+5.2%</span>
            <span class="ml-2 text-gray-500">vs last month</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Active Orders -->
    <div class="rounded-2xl border border-gray-200 bg-white p-5 dark:border-gray-800 dark:bg-white/[0.03] md:p-6 hover:shadow-lg transition-all duration-300">
      <div class="flex items-center justify-center w-12 h-12 bg-green-100 rounded-xl dark:bg-green-800/20">
        <svg class="fill-green-600 dark:fill-green-400" width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/>
          <polyline points="14,2 14,8 20,8"/>
          <line x1="16" y1="13" x2="8" y2="13"/>
          <line x1="16" y1="17" x2="8" y2="17"/>
          <polyline points="10,9 9,9 8,9"/>
        </svg>
      </div>

      <div class="flex items-end justify-between mt-5">
        <div>
          <span class="text-sm text-gray-500 dark:text-gray-400">Active Orders</span>
          <h4 class="mt-2 font-bold text-gray-800 text-title-sm dark:text-white/90">{{ formatNumber(activeOrders) }}</h4>
          <div class="flex items-center mt-2 text-sm">
            <span class="text-orange-600 font-medium">+12.8%</span>
            <span class="ml-2 text-gray-500">vs last week</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Available Storage -->
    <div class="rounded-2xl border border-gray-200 bg-white p-5 dark:border-gray-800 dark:bg-white/[0.03] md:p-6 hover:shadow-lg transition-all duration-300">
      <div class="flex items-center justify-center w-12 h-12 bg-purple-100 rounded-xl dark:bg-purple-800/20">
        <svg class="fill-purple-600 dark:fill-purple-400" width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <rect x="3" y="4" width="18" height="18" rx="2" ry="2"/>
          <line x1="9" y1="4" x2="9" y2="22"/>
          <line x1="15" y1="4" x2="15" y2="22"/>
          <line x1="3" y1="10" x2="21" y2="10"/>
          <line x1="3" y1="16" x2="21" y2="16"/>
        </svg>
      </div>

      <div class="flex items-end justify-between mt-5">
        <div>
          <span class="text-sm text-gray-500 dark:text-gray-400">Available Storage</span>
          <h4 class="mt-2 font-bold text-gray-800 text-title-sm dark:text-white/90">{{ storagePercentage }}%</h4>
          <div class="w-full bg-gray-200 rounded-full h-2 mt-3 dark:bg-gray-700">
            <div class="bg-purple-600 h-2 rounded-full transition-all duration-500" :style="`width: ${storagePercentage}%`"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- Pending Shipments -->
    <div class="rounded-2xl border border-gray-200 bg-white p-5 dark:border-gray-800 dark:bg-white/[0.03] md:p-6 hover:shadow-lg transition-all duration-300">
      <div class="flex items-center justify-center w-12 h-12 bg-orange-100 rounded-xl dark:bg-orange-800/20">
        <svg class="fill-orange-600 dark:fill-orange-400" width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <rect x="1" y="3" width="15" height="13"/>
          <polygon points="16,8 20,8 23,11 23,16 16,16"/>
          <circle cx="5.5" cy="18.5" r="2.5"/>
          <circle cx="18.5" cy="18.5" r="2.5"/>
        </svg>
      </div>

      <div class="flex items-end justify-between mt-5">
        <div>
          <span class="text-sm text-gray-500 dark:text-gray-400">Pending Shipments</span>
          <h4 class="mt-2 font-bold text-gray-800 text-title-sm dark:text-white/90">{{ formatNumber(pendingShipments) }}</h4>
          <div class="flex items-center mt-2 text-sm">
            <span class="text-red-600 font-medium">+3.1%</span>
            <span class="ml-2 text-gray-500">urgent attention</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// Reactive data
const totalInventory = ref(28567)
const activeOrders = ref(1247)
const pendingShipments = ref(89)
const storageCapacity = ref(75)

// Computed properties
const storagePercentage = computed(() => storageCapacity.value)

// Methods
const formatNumber = (num) => {
  return new Intl.NumberFormat().format(num)
}
</script>