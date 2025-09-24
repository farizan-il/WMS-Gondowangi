<template>
  <div class="space-y-6">
    <!-- Header KPI Cards -->
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-6 gap-4">
      <div v-for="kpi in kpiData" :key="kpi.id" @click="redirectToModule(kpi.module)" 
           class="bg-white dark:bg-gray-800 rounded-lg shadow p-6 cursor-pointer hover:shadow-lg transition-shadow border-l-4"
           :class="kpi.borderColor">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-sm font-medium text-gray-600 dark:text-gray-400">{{ kpi.title }}</p>
            <p class="text-3xl font-bold" :class="kpi.valueColor">{{ kpi.value }}</p>
            <p class="text-xs" :class="kpi.trendColor">{{ kpi.trend }}</p>
          </div>
          <div :class="kpi.iconBg" class="p-3 rounded-full">
            <svg class="w-6 h-6" :class="kpi.iconColor" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="kpi.iconPath"/>
            </svg>
          </div>
        </div>
      </div>
    </div>

    <div class="min-h-screen w-full">
      <!-- Full width & height card -->
      <div class="bg-white dark:bg-gray-800 rounded-lg shadow w-full h-full">
        <div class="p-6 border-b border-gray-200 dark:border-gray-700">
          <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Task Progress Kanban</h3>
        </div>
        <div class="p-4">
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 xl:grid-cols-7 gap-4 overflow-x-auto min-h-[400px]">
            <div v-for="column in kanbanColumns" :key="column.id" class="min-w-[200px]">
              <div class="bg-gray-50 dark:bg-gray-700 rounded-lg p-3 mb-3">
                <h4 class="font-medium text-gray-900 dark:text-white text-sm">{{ column.title }}</h4>
                <span class="text-xs text-gray-500 dark:text-gray-400">({{ column.tasks.length }})</span>
              </div>
              <div class="space-y-3 max-h-80 overflow-y-auto">
                <div v-for="task in column.tasks" :key="task.id" 
                     @click="openTaskModal(task)"
                     class="p-3 rounded-lg border cursor-pointer hover:shadow-md transition-shadow"
                     :class="getTaskCardClass(task.slaStatus)">
                  <div class="font-medium text-sm text-gray-900 dark:text-white mb-1">{{ task.title }}</div>
                  <div class="text-xs text-gray-600 dark:text-gray-400 mb-2">{{ task.item }}</div>
                  <div class="text-xs text-gray-600 dark:text-gray-400 mb-2">Qty: {{ task.qty }}</div>
                  <div class="flex justify-between items-center">
                    <span class="text-xs font-medium" :class="getSLAStatusColor(task.slaStatus)">
                      {{ task.status }}
                    </span>
                    <span class="text-xs text-gray-500 dark:text-gray-400">{{ task.duration }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- SLA Monitoring Chart (1/3 width) -->
      <div class="bg-white dark:bg-gray-800 rounded-lg shadow">
        <div class="p-6 border-b border-gray-200 dark:border-gray-700">
          <h3 class="text-lg font-semibold text-gray-900 dark:text-white">SLA Monitoring</h3>
        </div>
        <div class="p-4">
          <div class="space-y-4">
            <div v-for="sla in slaData" :key="sla.process" class="flex items-center justify-between">
              <div class="flex-1">
                <div class="flex justify-between mb-1">
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">{{ sla.process }}</span>
                  <span class="text-sm" :class="sla.status === 'over' ? 'text-red-600 dark:text-red-400' : 'text-green-600 dark:text-green-400'">
                    {{ sla.avgTime }}
                  </span>
                </div>
                <div class="w-full bg-gray-200 dark:bg-gray-700 rounded-full h-2">
                  <div class="h-2 rounded-full" :class="sla.status === 'over' ? 'bg-red-500' : 'bg-green-500'" 
                       :style="{width: sla.percentage + '%'}"></div>
                </div>
                <div class="text-xs text-gray-500 dark:text-gray-400 mt-1">
                  Target: {{ sla.target }} | {{ sla.status === 'over' ? 'OVER SLA' : 'ON TARGET' }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Notifications & Activities Section -->
    <div class="grid grid-cols-1 xl:grid-cols-2 gap-6">
      <!-- Critical Notifications -->
      <div class="bg-white dark:bg-gray-800 rounded-lg shadow">
        <div class="p-6 border-b border-gray-200 dark:border-gray-700">
          <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Notifikasi Kritis</h3>
        </div>
        <div class="p-4 max-h-80 overflow-y-auto">
          <div class="space-y-3">
            <div v-for="alert in criticalAlerts" :key="alert.id" 
                 @click="handleAlertClick(alert)"
                 class="p-4 rounded-lg border-l-4 cursor-pointer hover:bg-gray-50 dark:hover:bg-gray-700 transition-colors"
                 :class="getAlertClass(alert.type)">
              <div class="flex items-start justify-between">
                <div class="flex-1">
                  <div class="flex items-center mb-1">
                    <svg class="w-4 h-4 mr-2" :class="getAlertIconColor(alert.type)" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd" :d="getAlertIconPath(alert.type)" clip-rule="evenodd"/>
                    </svg>
                    <span class="text-sm font-medium text-gray-900 dark:text-white">{{ alert.title }}</span>
                  </div>
                  <p class="text-sm text-gray-600 dark:text-gray-400">{{ alert.message }}</p>
                  <p class="text-xs text-gray-500 dark:text-gray-500 mt-1">{{ alert.time }}</p>
                </div>
                <button class="text-gray-400 hover:text-gray-600 dark:hover:text-gray-300">
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"/>
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Recent Activities -->
      <div class="bg-white dark:bg-gray-800 rounded-lg shadow">
        <div class="p-6 border-b border-gray-200 dark:border-gray-700">
          <div class="flex justify-between items-center">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Aktivitas Terbaru</h3>
            <div class="flex space-x-2">
              <button @click="activityFilter = 'ALL'" :class="activityFilter === 'ALL' ? 'bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300' : 'text-gray-500 dark:text-gray-400'" class="px-2 py-1 text-xs rounded">All</button>
              <button @click="activityFilter = 'QC'" :class="activityFilter === 'QC' ? 'bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300' : 'text-gray-500 dark:text-gray-400'" class="px-2 py-1 text-xs rounded">QC</button>
              <button @click="activityFilter = 'PICK'" :class="activityFilter === 'PICK' ? 'bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300' : 'text-gray-500 dark:text-gray-400'" class="px-2 py-1 text-xs rounded">Pick</button>
              <button @click="activityFilter = 'PUT'" :class="activityFilter === 'PUT' ? 'bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300' : 'text-gray-500 dark:text-gray-400'" class="px-2 py-1 text-xs rounded">Put</button>
            </div>
          </div>
        </div>
        <div class="p-4 max-h-80 overflow-y-auto">
          <div class="space-y-3">
            <div v-for="activity in filteredActivities" :key="activity.id" 
                 class="flex items-start space-x-3 p-3 hover:bg-gray-50 dark:hover:bg-gray-700 rounded-lg transition-colors">
              <div class="flex-shrink-0">
                <div class="w-8 h-8 rounded-full flex items-center justify-center text-xs font-medium text-white"
                     :class="getActivityBadgeColor(activity.type)">
                  {{ activity.user }}
                </div>
              </div>
              <div class="flex-1 min-w-0">
                <div class="flex justify-between items-start">
                  <div>
                    <p class="text-sm text-gray-900 dark:text-white">{{ activity.activity }}</p>
                    <p class="text-xs text-gray-600 dark:text-gray-400">{{ activity.detail }}</p>
                  </div>
                  <span class="text-xs text-gray-500 dark:text-gray-500">{{ activity.time }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Task Detail Modal -->
    <div v-if="showTaskModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-2xl w-full mx-4">
        <div class="p-6">
          <div class="flex justify-between items-center mb-4">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Task Detail</h3>
            <button @click="closeTaskModal" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>
          
          <div v-if="selectedTask" class="space-y-4">
            <div class="grid grid-cols-2 gap-4">
              <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Task ID:</label>
                <p class="text-gray-900 dark:text-white">{{ selectedTask.title }}</p>
              </div>
              <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Status:</label>
                <p :class="getSLAStatusColor(selectedTask.slaStatus)">{{ selectedTask.status }}</p>
              </div>
              <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Item:</label>
                <p class="text-gray-900 dark:text-white">{{ selectedTask.item }}</p>
              </div>
              <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Quantity:</label>
                <p class="text-gray-900 dark:text-white">{{ selectedTask.qty }}</p>
              </div>
              <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Duration:</label>
                <p class="text-gray-900 dark:text-white">{{ selectedTask.duration }}</p>
              </div>
              <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Assigned To:</label>
                <p class="text-gray-900 dark:text-white">{{ selectedTask.assignedTo || 'Unassigned' }}</p>
              </div>
            </div>
            
            <div>
              <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Created At:</label>
              <p class="text-gray-900 dark:text-white">{{ selectedTask.createdAt }}</p>
            </div>
          </div>

          <div class="flex justify-end space-x-3 mt-6">
            <button @click="closeTaskModal" class="px-4 py-2 text-gray-700 dark:text-gray-300 bg-gray-200 dark:bg-gray-600 rounded-md hover:bg-gray-300 dark:hover:bg-gray-500">
              Close
            </button>
            <button @click="goToTaskDetail" class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">
              Go to Detail
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// Data reaktif
const showTaskModal = ref(false)
const selectedTask = ref(null)
const activityFilter = ref('ALL')

// KPI Data
const kpiData = ref([
  {
    id: 1,
    title: 'Incoming Today',
    value: 23,
    trend: '+15% vs yesterday',
    module: 'incoming',
    borderColor: 'border-blue-500',
    valueColor: 'text-blue-600 dark:text-blue-400',
    trendColor: 'text-green-500',
    iconBg: 'bg-blue-100 dark:bg-blue-900',
    iconColor: 'text-blue-600 dark:text-blue-400',
    iconPath: 'M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4'
  },
  {
    id: 2,
    title: 'QC Pending',
    value: 18,
    trend: 'High priority: 3',
    module: 'qc',
    borderColor: 'border-yellow-500',
    valueColor: 'text-yellow-600 dark:text-yellow-400',
    trendColor: 'text-red-500',
    iconBg: 'bg-yellow-100 dark:bg-yellow-900',
    iconColor: 'text-yellow-600 dark:text-yellow-400',
    iconPath: 'M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z'
  },
  {
    id: 3,
    title: 'Putaway Pending',
    value: 12,
    trend: 'Avg time: 2.5h',
    module: 'putaway',
    borderColor: 'border-purple-500',
    valueColor: 'text-purple-600 dark:text-purple-400',
    trendColor: 'text-yellow-500',
    iconBg: 'bg-purple-100 dark:bg-purple-900',
    iconColor: 'text-purple-600 dark:text-purple-400',
    iconPath: 'M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M9 19l3 3m0 0l3-3m-3 3V10'
  },
  {
    id: 4,
    title: 'Picking Active',
    value: 8,
    trend: '5 completed today',
    module: 'picking',
    borderColor: 'border-green-500',
    valueColor: 'text-green-600 dark:text-green-400',
    trendColor: 'text-green-500',
    iconBg: 'bg-green-100 dark:bg-green-900',
    iconColor: 'text-green-600 dark:text-green-400',
    iconPath: 'M9 5H7a2 2 0 00-2 2v0a2 2 0 002 2h2m0 0h2a2 2 0 012 2v0a2 2 0 01-2 2H9m0-4h2m-2-8a2 2 0 00-2 2v0a2 2 0 002 2h2a2 2 0 012-2v0a2 2 0 00-2-2H9z'
  },
  {
    id: 5,
    title: 'Return Pending',
    value: 5,
    trend: '2 to suppliers',
    module: 'return',
    borderColor: 'border-red-500',
    valueColor: 'text-red-600 dark:text-red-400',
    trendColor: 'text-gray-500',
    iconBg: 'bg-red-100 dark:bg-red-900',
    iconColor: 'text-red-600 dark:text-red-400',
    iconPath: 'M3 10h10a8 8 0 018 8v2M3 10l6 6m-6-6l6-6'
  },
  {
    id: 6,
    title: 'Critical Stock',
    value: 7,
    trend: 'Below min level',
    module: 'stock',
    borderColor: 'border-orange-500',
    valueColor: 'text-orange-600 dark:text-orange-400',
    trendColor: 'text-red-500',
    iconBg: 'bg-orange-100 dark:bg-orange-900',
    iconColor: 'text-orange-600 dark:text-orange-400',
    iconPath: 'M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z'
  }
])

// Kanban Data
const kanbanColumns = ref([
  {
    id: 'incoming',
    title: 'Incoming',
    tasks: [
      { id: 'in1', title: 'Shipment #IN001', item: 'RM-001', qty: '500kg', status: 'Processing', duration: '45m', slaStatus: 'normal', assignedTo: 'WH001', createdAt: '2025-09-19 08:30' },
      { id: 'in2', title: 'Shipment #IN002', item: 'PM-001', qty: '200pcs', status: 'Pending', duration: '1h 20m', slaStatus: 'warning', assignedTo: 'WH002', createdAt: '2025-09-19 07:15' }
    ]
  },
  {
    id: 'qc',
    title: 'QC',
    tasks: [
      { id: 'qc1', title: 'Lot #RM001/B123', item: 'RM-001', qty: '500kg', status: 'Pending QC', duration: '2h 15m', slaStatus: 'over', assignedTo: 'QC001', createdAt: '2025-09-19 06:00' },
      { id: 'qc2', title: 'Lot #PM002/B456', item: 'PM-002', qty: '100pcs', status: 'In QC', duration: '1h 30m', slaStatus: 'normal', assignedTo: 'QC002', createdAt: '2025-09-19 07:45' }
    ]
  },
  {
    id: 'karantina',
    title: 'Karantina',
    tasks: [
      { id: 'kar1', title: 'Lot #RM003/B789', item: 'RM-003', qty: '250kg', status: 'Karantina', duration: '4h 30m', slaStatus: 'normal', assignedTo: null, createdAt: '2025-09-19 04:15' }
    ]
  },
  {
    id: 'putaway',
    title: 'Putaway',
    tasks: [
      { id: 'put1', title: 'TO #PUT001', item: 'RM-004', qty: '300kg', status: 'Pending Put', duration: '2h 45m', slaStatus: 'warning', assignedTo: 'WH003', createdAt: '2025-09-19 06:30' },
      { id: 'put2', title: 'TO #PUT002', item: 'PM-003', qty: '150pcs', status: 'In Progress', duration: '1h 10m', slaStatus: 'normal', assignedTo: 'WH001', createdAt: '2025-09-19 08:05' }
    ]
  },
  {
    id: 'reservation',
    title: 'Reservation',
    tasks: [
      { id: 'res1', title: 'RSV #R001', item: 'Multiple Items', qty: '5 items', status: 'Pending Approval', duration: '30m', slaStatus: 'normal', assignedTo: 'SUP001', createdAt: '2025-09-19 08:45' }
    ]
  },
  {
    id: 'picking',
    title: 'Picking',
    tasks: [
      { id: 'pic1', title: 'TO #PICK001', item: 'RM-005', qty: '200kg', status: 'Picking', duration: '1h 45m', slaStatus: 'normal', assignedTo: 'WH004', createdAt: '2025-09-19 07:30' },
      { id: 'pic2', title: 'TO #PICK002', item: 'PM-004', qty: '80pcs', status: 'Short Pick', duration: '3h 20m', slaStatus: 'over', assignedTo: 'WH002', createdAt: '2025-09-19 05:55' }
    ]
  },
  {
    id: 'return',
    title: 'Return',
    tasks: [
      { id: 'ret1', title: 'RTS #RET001', item: 'RM-006', qty: '50kg', status: 'Pending Return', duration: '24h', slaStatus: 'over', assignedTo: 'WH005', createdAt: '2025-09-18 09:15' }
    ]
  }
])

// SLA Data
const slaData = ref([
  { process: 'Incoming', avgTime: '1h 15m', target: '2h', percentage: 62, status: 'ok' },
  { process: 'QC Check', avgTime: '1h 45m', target: '2h', percentage: 87, status: 'ok' },
  { process: 'Putaway', avgTime: '2h 30m', target: '3h', percentage: 83, status: 'ok' },
  { process: 'Picking', avgTime: '4h 30m', target: '3h', percentage: 150, status: 'over' },
  { process: 'Return Process', avgTime: '6h 15m', target: '4h', percentage: 156, status: 'over' }
])

// Critical Alerts
const criticalAlerts = ref([
  {
    id: 1,
    type: 'stock',
    title: 'Stock Alert',
    message: 'RM-002 tinggal 50kg (di bawah min level 200kg)',
    time: '10 minutes ago',
    module: 'stock'
  },
  {
    id: 2,
    type: 'expiry',
    title: 'Expiry Alert',
    message: 'Lot RM-005/B789 kadaluarsa dalam 20 hari',
    time: '25 minutes ago',
    module: 'stock'
  },
  {
    id: 3,
    type: 'delay',
    title: 'Delay Alert',
    message: 'Shipment PO456 pending QC > 3 jam',
    time: '1 hour ago',
    module: 'qc'
  },
  {
    id: 4,
    type: 'capacity',
    title: 'Capacity Alert',
    message: 'Bin A-12 sudah 95% penuh',
    time: '2 hours ago',
    module: 'storage'
  }
])

// Activities Data
const activities = ref([
  { id: 1, time: '09:15', user: 'QC01', activity: 'QC PASS', detail: 'Lot RM-001/Batch123 → Karantina', type: 'QC' },
  { id: 2, time: '09:45', user: 'WH01', activity: 'Putaway Completed', detail: 'TO/2025/0007 Completed', type: 'PUT' },
  { id: 3, time: '10:20', user: 'PD01', activity: 'Request RM', detail: 'RSV/2025/009 Submitted', type: 'REQ' },
  { id: 4, time: '10:35', user: 'WH02', activity: 'Picking Started', detail: 'TO/PICK/001 - 5 items', type: 'PICK' },
  { id: 5, time: '10:50', user: 'QC02', activity: 'QC REJECT', detail: 'Lot PM-003/B456 → Return', type: 'QC' },
  { id: 6, time: '11:10', user: 'WH03', activity: 'Putaway Started', detail: 'TO/PUT/003 - Bin A-15', type: 'PUT' },
  { id: 7, time: '11:25', user: 'WH04', activity: 'Picking Completed', detail: 'TO/PICK/002 - Short Pick', type: 'PICK' }
])

// Computed
const filteredActivities = computed(() => {
  if (activityFilter.value === 'ALL') return activities.value
  return activities.value.filter(activity => activity.type === activityFilter.value)
})

// Methods
const redirectToModule = (module) => {
  alert(`Redirect to ${module} module`)
}

const getTaskCardClass = (slaStatus) => {
  const classes = {
    'normal': 'bg-white dark:bg-gray-700 border-gray-200 dark:border-gray-600',
    'warning': 'bg-yellow-50 dark:bg-yellow-900 border-yellow-200 dark:border-yellow-700',
    'over': 'bg-red-50 dark:bg-red-900 border-red-200 dark:border-red-700'
  }
  return classes[slaStatus] || classes.normal
}

const getSLAStatusColor = (status) => {
  if (status.includes('Over') || status.includes('Pending') || status.includes('Short')) {
    return 'text-red-600 dark:text-red-400'
  }
  if (status.includes('Warning') || status.includes('QC')) {
    return 'text-yellow-600 dark:text-yellow-400'
  }
  return 'text-green-600 dark:text-green-400'
}

const getAlertClass = (type) => {
  const classes = {
    'stock': 'border-orange-500 bg-orange-50 dark:bg-orange-900',
    'expiry': 'border-yellow-500 bg-yellow-50 dark:bg-yellow-900',
    'delay': 'border-red-500 bg-red-50 dark:bg-red-900',
    'capacity': 'border-purple-500 bg-purple-50 dark:bg-purple-900'
  }
  return classes[type] || 'border-gray-500 bg-gray-50 dark:bg-gray-800'
}

const getAlertIconColor = (type) => {
  const colors = {
    'stock': 'text-orange-600 dark:text-orange-400',
    'expiry': 'text-yellow-600 dark:text-yellow-400',
    'delay': 'text-red-600 dark:text-red-400',
    'capacity': 'text-purple-600 dark:text-purple-400'
  }
  return colors[type] || 'text-gray-600 dark:text-gray-400'
}

const getAlertIconPath = (type) => {
  const paths = {
    'stock': 'M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z',
    'expiry': 'M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z',
    'delay': 'M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z',
    'capacity': 'M5 8a2 2 0 012-2h6a2 2 0 012 2v6a2 2 0 01-2 2H7a2 2 0 01-2-2V8zM5 8V6a2 2 0 012-2h6a2 2 0 012 2v2m0 0V4a2 2 0 00-2-2H9a2 2 0 00-2 2v4.01'
  }
  return paths[type] || paths.stock
}

const getActivityBadgeColor = (type) => {
  const colors = {
    'QC': 'bg-yellow-500',
    'PUT': 'bg-purple-500',
    'PICK': 'bg-green-500',
    'REQ': 'bg-blue-500'
  }
  return colors[type] || 'bg-gray-500'
}

const openTaskModal = (task) => {
  selectedTask.value = task
  showTaskModal.value = true
}

const closeTaskModal = () => {
  showTaskModal.value = false
  selectedTask.value = null
}

const goToTaskDetail = () => {
  if (selectedTask.value) {
    alert(`Navigating to detailed view for task: ${selectedTask.value.title}`)
    closeTaskModal()
  }
}

const handleAlertClick = (alert) => {
  alert(`Navigating to ${alert.module} module for: ${alert.title}`)
}

// Real-time updates simulation
let updateInterval = null

const startRealTimeUpdates = () => {
  updateInterval = setInterval(() => {
    // Simulate real-time updates
    updateKPIData()
    updateTaskStatus()
    addNewActivity()
  }, 30000) // Update every 30 seconds
}

const updateKPIData = () => {
  // Simulate KPI updates
  kpiData.value.forEach(kpi => {
    const change = Math.floor(Math.random() * 3) - 1 // -1, 0, or 1
    kpi.value = Math.max(0, kpi.value + change)
  })
}

const updateTaskStatus = () => {
  // Simulate task progress
  kanbanColumns.value.forEach(column => {
    column.tasks.forEach(task => {
      // Randomly update duration
      const currentMinutes = parseInt(task.duration.match(/(\d+)h?\s*(\d+)?m?/)?.[1] || 0) * 60 + 
                            parseInt(task.duration.match(/(\d+)m/)?.[1] || 0)
      const newMinutes = currentMinutes + Math.floor(Math.random() * 5)
      const hours = Math.floor(newMinutes / 60)
      const minutes = newMinutes % 60
      task.duration = hours > 0 ? `${hours}h ${minutes}m` : `${minutes}m`
      
      // Update SLA status based on duration
      if (newMinutes > 180) { // Over 3 hours
        task.slaStatus = 'over'
      } else if (newMinutes > 120) { // Over 2 hours
        task.slaStatus = 'warning'
      } else {
        task.slaStatus = 'normal'
      }
    })
  })
}

const addNewActivity = () => {
  // Simulate new activity
  const users = ['WH01', 'WH02', 'QC01', 'QC02', 'PD01']
  const activityTypes = ['QC', 'PUT', 'PICK', 'REQ']
  const activityTexts = {
    'QC': ['QC PASS', 'QC REJECT', 'QC Started'],
    'PUT': ['Putaway Completed', 'Putaway Started'],
    'PICK': ['Picking Completed', 'Picking Started'],
    'REQ': ['Request Created', 'Request Approved']
  }
  
  const randomType = activityTypes[Math.floor(Math.random() * activityTypes.length)]
  const randomUser = users[Math.floor(Math.random() * users.length)]
  const randomActivity = activityTexts[randomType][Math.floor(Math.random() * activityTexts[randomType].length)]
  
  const newActivity = {
    id: activities.value.length + 1,
    time: new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }),
    user: randomUser,
    activity: randomActivity,
    detail: `Auto-generated activity ${Date.now()}`,
    type: randomType
  }
  
  activities.value.unshift(newActivity)
  
  // Keep only last 10 activities
  if (activities.value.length > 10) {
    activities.value = activities.value.slice(0, 10)
  }
}

// Lifecycle
onMounted(() => {
  startRealTimeUpdates()
})

onUnmounted(() => {
  if (updateInterval) {
    clearInterval(updateInterval)
  }
})
</script>