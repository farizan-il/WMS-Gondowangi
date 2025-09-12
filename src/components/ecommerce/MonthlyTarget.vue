<template>
  <div class="rounded-2xl border border-gray-200 bg-white p-5 dark:border-gray-800 dark:bg-white/[0.03] sm:p-6">
    <div class="flex justify-between mb-6">
      <div>
        <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">
          Warehouse Locations
        </h3>
        <p class="mt-1 text-gray-500 text-theme-sm dark:text-gray-400">
          Distribution centers and fulfillment status
        </p>
      </div>
    </div>
    
    <!-- Interactive Warehouse Map -->
    <div class="px-4 py-6 my-6 overflow-hidden border border-gary-200 rounded-2xl bg-gray-50 dark:border-gray-800 dark:bg-gray-900 sm:px-6">
      <div
        ref="mapRef"
        id="warehouseMap"
        class="mapOne map-btn -mx-4 -my-6 h-[240px] w-full sm:-mx-6"
      ></div>
    </div>

    <!-- Warehouse Status List -->
    <div class="space-y-4">
      <div 
        v-for="(warehouse, index) in warehouses" 
        :key="index"
        class="flex items-center justify-between p-4 rounded-lg border border-gray-100 hover:border-gray-200 hover:shadow-sm transition-all duration-200 dark:border-gray-800 dark:hover:border-gray-700"
      >
        <div class="flex items-center gap-4">
          <div class="flex items-center justify-center w-10 h-10 rounded-lg" :class="warehouse.statusColor">
            <svg class="w-5 h-5 text-white" fill="currentColor" viewBox="0 0 20 20">
              <path fillRule="evenodd" d="M4 4a2 2 0 00-2 2v4a2 2 0 002 2V6h10a2 2 0 00-2-2H4zm2 6a2 2 0 012-2h8a2 2 0 012 2v4a2 2 0 01-2 2H8a2 2 0 01-2-2v-4zm6 4a2 2 0 100-4 2 2 0 000 4z" clipRule="evenodd" />
            </svg>
          </div>
          <div>
            <p class="font-semibold text-gray-800 text-theme-sm dark:text-white/90">{{ warehouse.name }}</p>
            <div class="flex items-center gap-4 mt-1">
              <span class="text-gray-500 text-theme-xs dark:text-gray-400">
                {{ warehouse.location }}
              </span>
              <span class="px-2 py-1 text-xs rounded-full" :class="warehouse.statusBadge">
                {{ warehouse.status }}
              </span>
            </div>
          </div>
        </div>

        <div class="flex items-center gap-6">
          <div class="text-right">
            <p class="text-sm font-medium text-gray-800 dark:text-white/90">{{ warehouse.utilization }}%</p>
            <p class="text-xs text-gray-500 dark:text-gray-400">Capacity</p>
          </div>
          
          <div class="w-24">
            <div class="flex items-center justify-between text-xs text-gray-600 dark:text-gray-400 mb-1">
              <span>{{ warehouse.utilization }}%</span>
            </div>
            <div class="w-full bg-gray-200 rounded-full h-2 dark:bg-gray-700">
              <div 
                class="h-2 rounded-full transition-all duration-500" 
                :class="warehouse.progressColor"
                :style="`width: ${warehouse.utilization}%`"
              ></div>
            </div>
          </div>

          <button class="p-2 text-gray-400 hover:text-gray-600 dark:hover:text-gray-300 transition-colors">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M9 5l7 7-7 7" />
            </svg>
          </button>
        </div>
      </div>
    </div>

    <!-- Quick Stats -->
    <div class="grid grid-cols-3 gap-4 mt-6 pt-6 border-t border-gray-100 dark:border-gray-800">
      <div class="text-center">
        <p class="text-2xl font-bold text-gray-800 dark:text-white/90">{{ totalWarehouses }}</p>
        <p class="text-sm text-gray-500 dark:text-gray-400">Total Locations</p>
      </div>
      <div class="text-center">
        <p class="text-2xl font-bold text-green-600 dark:text-green-400">{{ activeWarehouses }}</p>
        <p class="text-sm text-gray-500 dark:text-gray-400">Active</p>
      </div>
      <div class="text-center">
        <p class="text-2xl font-bold text-gray-800 dark:text-white/90">{{ averageUtilization }}%</p>
        <p class="text-sm text-gray-500 dark:text-gray-400">Avg. Utilization</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, computed } from 'vue'
import jsVectorMap from 'jsvectormap'
import 'jsvectormap/dist/maps/world'

const mapRef = ref(null)
const mapInstance = ref(null)

const warehouses = ref([
  {
    name: 'Main Distribution Center',
    location: 'Jakarta, Indonesia',
    status: 'Operational',
    utilization: 87,
    statusColor: 'bg-green-500',
    statusBadge: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400',
    progressColor: 'bg-green-500'
  },
  {
    name: 'Regional Hub East',
    location: 'Surabaya, Indonesia',
    status: 'Operational',
    utilization: 73,
    statusColor: 'bg-green-500',
    statusBadge: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400',
    progressColor: 'bg-green-500'
  },
  {
    name: 'Northern Warehouse',
    location: 'Medan, Indonesia',
    status: 'Maintenance',
    utilization: 45,
    statusColor: 'bg-orange-500',
    statusBadge: 'bg-orange-100 text-orange-800 dark:bg-orange-900/30 dark:text-orange-400',
    progressColor: 'bg-orange-500'
  },
  {
    name: 'Central Fulfillment',
    location: 'Bandung, Indonesia',
    status: 'Operational',
    utilization: 92,
    statusColor: 'bg-green-500',
    statusBadge: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400',
    progressColor: 'bg-red-500'
  },
])

const totalWarehouses = computed(() => warehouses.value.length)
const activeWarehouses = computed(() => warehouses.value.filter(w => w.status === 'Operational').length)
const averageUtilization = computed(() => {
  const total = warehouses.value.reduce((sum, w) => sum + w.utilization, 0)
  return Math.round(total / warehouses.value.length)
})

const initMap = () => {
  if (mapRef.value) {
    mapInstance.value = new jsVectorMap({
      selector: mapRef.value,
      map: 'world',
      zoomButtons: true,
      zoomOnScroll: false,
      regionStyle: {
        initial: {
          fontFamily: 'Outfit',
          fill: '#E5E7EB',
          stroke: '#D1D5DB',
          strokeWidth: 0.5,
        },
        hover: {
          fillOpacity: 1,
          fill: '#3B82F6',
        },
      },
      backgroundColor: 'transparent',
      markers: [
        {
          name: 'Jakarta DC',
          coords: [-6.2088, 106.8456],
        },
        {
          name: 'Surabaya Hub',
          coords: [-7.2575, 112.7521],
        },
        {
          name: 'Medan Warehouse',
          coords: [3.5952, 98.6722],
        },
        {
          name: 'Bandung Center',
          coords: [-6.9175, 107.6191],
        },
      ],
      markerStyle: {
        initial: {
          strokeWidth: 2,
          fill: '#3B82F6',
          fillOpacity: 1,
          stroke: '#FFFFFF',
          r: 6,
        },
        hover: {
          fill: '#1D4ED8',
          fillOpacity: 1,
          stroke: '#FFFFFF',
          strokeWidth: 3,
          r: 8,
        },
      },
      onMarkerTooltipShow: function (event, tooltip, code) {
        const markerNames = {
          0: 'Jakarta DC - 87% Capacity',
          1: 'Surabaya Hub - 73% Capacity', 
          2: 'Medan Warehouse - 45% Capacity',
          3: 'Bandung Center - 92% Capacity'
        }
        tooltip.text(markerNames[code] || 'Warehouse Location')
      },
    })
  }
}

onMounted(() => {
  initMap()
})
</script>