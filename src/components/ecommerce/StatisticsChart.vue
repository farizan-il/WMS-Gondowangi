<template>
  <div
    class="overflow-hidden rounded-2xl border border-gray-200 bg-white px-4 pb-3 pt-4 dark:border-gray-800 dark:bg-white/[0.03] sm:px-6"
  >
    <div class="flex flex-col gap-2 mb-4 sm:flex-row sm:items-center sm:justify-between">
      <div>
        <h3 class="text-lg font-semibold text-gray-800 dark:text-white/90">Recent Activities</h3>
        <p class="text-sm text-gray-500 dark:text-gray-400">Latest warehouse operations and transactions</p>
      </div>

      <div class="flex items-center gap-3">
        <div class="relative">
          <select 
            v-model="selectedFilter"
            @change="filterActivities"
            class="appearance-none bg-white dark:bg-gray-800 border border-gray-300 dark:border-gray-700 rounded-lg px-4 py-2.5 pr-8 text-sm font-medium text-gray-700 dark:text-gray-300 focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
            <option value="all">All Activities</option>
            <option value="inbound">Inbound</option>
            <option value="outbound">Outbound</option>
            <option value="transfer">Transfer</option>
            <option value="adjustment">Adjustment</option>
          </select>
          <div class="absolute inset-y-0 right-0 flex items-center px-2 pointer-events-none">
            <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M19 9l-7 7-7-7" />
            </svg>
          </div>
        </div>

        <button
          @click="refreshData"
          class="inline-flex items-center gap-2 rounded-lg border border-gray-300 bg-white px-4 py-2.5 text-theme-sm font-medium text-gray-700 shadow-theme-xs hover:bg-gray-50 hover:text-gray-800 dark:border-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:hover:bg-white/[0.03] dark:hover:text-gray-200 transition-all duration-200"
        >
          <svg class="w-4 h-4" :class="{ 'animate-spin': isRefreshing }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
          </svg>
          Refresh
        </button>
      </div>
    </div>

    <div class="max-w-full overflow-x-auto custom-scrollbar">
      <table class="min-w-full">
        <thead>
          <tr class="border-t border-gray-100 dark:border-gray-800">
            <th class="py-3 text-left">
              <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Activity</p>
            </th>
            <th class="py-3 text-left">
              <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Type</p>
            </th>
            <th class="py-3 text-left">
              <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Quantity</p>
            </th>
            <th class="py-3 text-left">
              <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Location</p>
            </th>
            <th class="py-3 text-left">
              <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Time</p>
            </th>
            <th class="py-3 text-left">
              <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Status</p>
            </th>
            <th class="py-3 text-left">
              <p class="font-medium text-gray-500 text-theme-xs dark:text-gray-400">Actions</p>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(activity, index) in filteredActivities"
            :key="index"
            class="border-t border-gray-100 dark:border-gray-800 hover:bg-gray-50 dark:hover:bg-gray-900/50 transition-colors duration-150"
          >
            <td class="py-3 whitespace-nowrap">
              <div class="flex items-center gap-3">
                <div class="flex items-center justify-center w-10 h-10 rounded-lg" :class="activity.iconBg">
                  <svg class="w-5 h-5 text-white" fill="currentColor" viewBox="0 0 20 20">
                    <path v-html="activity.icon" />
                  </svg>
                </div>
                <div>
                  <p class="font-medium text-gray-800 text-theme-sm dark:text-white/90">
                    {{ activity.description }}
                  </p>
                  <span class="text-gray-500 text-theme-xs dark:text-gray-400">
                    {{ activity.itemCode }}
                  </span>
                </div>
              </div>
            </td>
            <td class="py-3 whitespace-nowrap">
              <span 
                class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium"
                :class="activity.typeBadge"
              >
                {{ activity.type }}
              </span>
            </td>
            <td class="py-3 whitespace-nowrap">
              <p class="text-gray-800 text-theme-sm font-medium dark:text-white/90">{{ activity.quantity }}</p>
            </td>
            <td class="py-3 whitespace-nowrap">
              <p class="text-gray-500 text-theme-sm dark:text-gray-400">{{ activity.location }}</p>
            </td>
            <td class="py-3 whitespace-nowrap">
              <div>
                <p class="text-gray-800 text-theme-sm dark:text-white/90">{{ activity.time }}</p>
                <p class="text-gray-500 text-theme-xs dark:text-gray-400">{{ activity.date }}</p>
              </div>
            </td>
            <td class="py-3 whitespace-nowrap">
              <span
                class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium"
                :class="activity.statusBadge"
              >
                <span class="w-1.5 h-1.5 rounded-full mr-1.5" :class="activity.statusDot"></span>
                {{ activity.status }}
              </span>
            </td>
            <td class="py-3 whitespace-nowrap">
              <div class="flex items-center gap-2">
                <button 
                  @click="viewDetails(activity)"
                  class="p-1.5 text-gray-400 hover:text-blue-600 dark:hover:text-blue-400 transition-colors duration-150"
                  title="View Details"
                >
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                  </svg>
                </button>
                <button 
                  @click="downloadReport(activity)"
                  class="p-1.5 text-gray-400 hover:text-green-600 dark:hover:text-green-400 transition-colors duration-150"
                  title="Download Report"
                >
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                  </svg>
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Pagination -->
    <div class="flex items-center justify-between mt-6 pt-4 border-t border-gray-100 dark:border-gray-800">
      <div class="text-sm text-gray-500 dark:text-gray-400">
        Showing {{ (currentPage - 1) * itemsPerPage + 1 }} to {{ Math.min(currentPage * itemsPerPage, totalItems) }} of {{ totalItems }} results
      </div>
      <div class="flex items-center gap-2">
        <button 
          @click="previousPage"
          :disabled="currentPage === 1"
          class="px-3 py-1.5 text-sm font-medium text-gray-500 bg-white border border-gray-300 rounded-md hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed dark:bg-gray-800 dark:border-gray-700 dark:text-gray-400 dark:hover:bg-gray-700"
        >
          Previous
        </button>
        <span class="px-3 py-1.5 text-sm font-medium text-gray-700 bg-gray-100 border border-gray-300 rounded-md dark:bg-gray-700 dark:border-gray-600 dark:text-gray-300">
          {{ currentPage }}
        </span>
        <button 
          @click="nextPage"
          :disabled="currentPage === totalPages"
          class="px-3 py-1.5 text-sm font-medium text-gray-500 bg-white border border-gray-300 rounded-md hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed dark:bg-gray-800 dark:border-gray-700 dark:text-gray-400 dark:hover:bg-gray-700"
        >
          Next
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const selectedFilter = ref('all')
const isRefreshing = ref(false)
const currentPage = ref(1)
const itemsPerPage = 8

const activities = ref([
  {
    description: 'Received Electronics Shipment',
    itemCode: 'SKU-EL-001',
    type: 'Inbound',
    quantity: '1,250 units',
    location: 'Dock A-1',
    time: '14:30',
    date: 'Today',
    status: 'Completed',
    iconBg: 'bg-blue-500',
    icon: `<path fillRule="evenodd" d="M3 3a1 1 0 000 2v8a2 2 0 002 2h2.586l-1.293 1.293a1 1 0 101.414 1.414L10 15.414l2.293 2.293a1 1 0 001.414-1.414L12.414 15H15a2 2 0 002-2V5a1 1 0 100-2H3zm11.707 4.707a1 1 0 00-1.414-1.414L10 9.586 8.707 8.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />`,
    typeBadge: 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-400',
    statusBadge: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400',
    statusDot: 'bg-green-400'
  },
  {
    description: 'Furniture Items Shipped',
    itemCode: 'SKU-FR-045',
    type: 'Outbound',
    quantity: '89 units',
    location: 'Zone C-2',
    time: '13:15',
    date: 'Today',
    status: 'In Transit',
    iconBg: 'bg-green-500',
    icon: `<path d="M1 3a1 1 0 000 2h1.22l.305 1.222a.997.997 0 00.01.042l1.358 5.43-.893.892C3.74 12.846 4.632 14 6 14a1 1 0 000-2H4.28l.01-.057L5.022 11h3.3c.3 0 .573-.14.75-.36L14.282 4H16a1 1 0 000-2H1z"/>`,
    typeBadge: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400',
    statusBadge: 'bg-orange-100 text-orange-800 dark:bg-orange-900/30 dark:text-orange-400',
    statusDot: 'bg-orange-400'
  },
  {
    description: 'Inventory Count Adjustment',
    itemCode: 'SKU-TX-022',
    type: 'Adjustment',
    quantity: '+45 units',
    location: 'Section B-3',
    time: '12:45',
    date: 'Today',
    status: 'Pending Review',
    iconBg: 'bg-purple-500',
    icon: `<path fillRule="evenodd" d="M4 2a1 1 0 011 1v2.101a7.002 7.002 0 0111.601 2.566 1 1 0 11-1.885.666A5.002 5.002 0 005.999 7H9a1 1 0 010 2H4a1 1 0 01-1-1V3a1 1 0 011-1zm.008 9.057a1 1 0 011.276.61A5.002 5.002 0 0014.001 13H11a1 1 0 110-2h5a1 1 0 011 1v5a1 1 0 11-2 0v-2.101a7.002 7.002 0 01-11.601-2.566 1 1 0 01.61-1.276z" clipRule="evenodd" />`,
    typeBadge: 'bg-purple-100 text-purple-800 dark:bg-purple-900/30 dark:text-purple-400',
    statusBadge: 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900/30 dark:text-yellow-400',
    statusDot: 'bg-yellow-400'
  },
  {
    description: 'Stock Transfer to Branch',
    itemCode: 'SKU-AP-089',
    type: 'Transfer',
    quantity: '320 units',
    location: 'Warehouse 2',
    time: '11:20',
    date: 'Today',
    status: 'Completed',
    iconBg: 'bg-indigo-500',
    icon: `<path fillRule="evenodd" d="M7.707 3.293a1 1 0 010 1.414L5.414 7H11a7 7 0 017 7v2a1 1 0 11-2 0v-2a5 5 0 00-5-5H5.414l2.293 2.293a1 1 0 11-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z" clipRule="evenodd" />`,
    typeBadge: 'bg-indigo-100 text-indigo-800 dark:bg-indigo-900/30 dark:text-indigo-400',
    statusBadge: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400',
    statusDot: 'bg-green-400'
  },
  {
    description: 'Emergency Restock Request',
    itemCode: 'SKU-MED-156',
    type: 'Inbound',
    quantity: '500 units',
    location: 'Priority Zone',
    time: '10:30',
    date: 'Today',
    status: 'Urgent',
    iconBg: 'bg-red-500',
    icon: `<path fillRule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clipRule="evenodd" />`,
    typeBadge: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-400',
    statusBadge: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-400',
    statusDot: 'bg-red-400'
  },
  {
    description: 'Quality Check Completed',
    itemCode: 'SKU-QC-078',
    type: 'Inspection',
    quantity: '150 units',
    location: 'QC Station 1',
    time: '09:45',
    date: 'Today',
    status: 'Approved',
    iconBg: 'bg-teal-500',
    icon: `<path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" />`,
    typeBadge: 'bg-teal-100 text-teal-800 dark:bg-teal-900/30 dark:text-teal-400',
    statusBadge: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-400',
    statusDot: 'bg-green-400'
  },
  {
    description: 'Bulk Order Preparation',
    itemCode: 'SKU-BLK-234',
    type: 'Outbound',
    quantity: '2,500 units',
    location: 'Staging Area',
    time: '08:15',
    date: 'Yesterday',
    status: 'In Progress',
    iconBg: 'bg-orange-500',
    icon: `<path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4zM3 10a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H4a1 1 0 01-1-1v-6zM14 9a1 1 0 00-1 1v6a1 1 0 001 1h2a1 1 0 001-1v-6a1 1 0 00-1-1h-2z" />`,
    typeBadge: 'bg-orange-100 text-orange-800 dark:bg-orange-900/30 dark:text-orange-400',
    statusBadge: 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-400',
    statusDot: 'bg-blue-400'
  },
  {
    description: 'Damaged Items Quarantine',
    itemCode: 'SKU-DMG-901',
    type: 'Adjustment',
    quantity: '-12 units',
    location: 'Quarantine Zone',
    time: '16:20',
    date: 'Yesterday',
    status: 'Reviewed',
    iconBg: 'bg-gray-500',
    icon: `<path fillRule="evenodd" d="M13.477 14.89A6 6 0 015.11 6.524l8.367 8.368zm1.414-1.414L6.524 5.11a6 6 0 018.367 8.367zM4 10a6 6 0 1112 0A6 6 0 014 10z" clipRule="evenodd" />`,
    typeBadge: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-400',
    statusBadge: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-400',
    statusDot: 'bg-gray-400'
  }
])

const filteredActivities = computed(() => {
  if (selectedFilter.value === 'all') {
    return activities.value.slice((currentPage.value - 1) * itemsPerPage, currentPage.value * itemsPerPage)
  }
  const filtered = activities.value.filter(activity => 
    activity.type.toLowerCase() === selectedFilter.value.toLowerCase()
  )
  return filtered.slice((currentPage.value - 1) * itemsPerPage, currentPage.value * itemsPerPage)
})

const totalItems = computed(() => {
  if (selectedFilter.value === 'all') {
    return activities.value.length
  }
  return activities.value.filter(activity => 
    activity.type.toLowerCase() === selectedFilter.value.toLowerCase()
  ).length
})

const totalPages = computed(() => Math.ceil(totalItems.value / itemsPerPage))

const filterActivities = () => {
  currentPage.value = 1
}

const refreshData = async () => {
  isRefreshing.value = true
  // Simulate API call
  await new Promise(resolve => setTimeout(resolve, 1000))
  isRefreshing.value = false
}

const viewDetails = (activity) => {
  console.log('Viewing details for:', activity.description)
  // Implement detail view functionality
}

const downloadReport = (activity) => {
  console.log('Downloading report for:', activity.description)
  // Implement download functionality
}

const previousPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--
  }
}

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++
  }
}

onMounted(() => {
  // Initialize component
})
</script>