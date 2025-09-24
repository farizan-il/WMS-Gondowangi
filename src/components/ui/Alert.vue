<template>
  <div class="space-y-6">
    <!-- Header -->
    <div class="flex justify-between items-center">
      <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Picking List (Transfer Order)</h2>
      <div class="text-sm text-gray-600 dark:text-gray-400">
        Total picking tasks: {{ pickingTasks.length }}
      </div>
    </div>

    <!-- Filter Status -->
    <div class="bg-white dark:bg-gray-800 rounded-lg p-4 shadow">
      <div class="flex items-center space-x-4">
        <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Filter Status:</span>
        <div class="flex space-x-2">
          <button @click="filterStatus = 'ALL'" :class="filterStatus === 'ALL' ? 'bg-blue-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-blue-500 hover:text-white">
            Semua
          </button>
          <button @click="filterStatus = 'Pending'" :class="filterStatus === 'Pending' ? 'bg-gray-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-gray-500 hover:text-white">
            Pending
          </button>
          <button @click="filterStatus = 'In Progress'" :class="filterStatus === 'In Progress' ? 'bg-yellow-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-yellow-500 hover:text-white">
            In Progress
          </button>
          <button @click="filterStatus = 'Completed'" :class="filterStatus === 'Completed' ? 'bg-green-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-green-500 hover:text-white">
            Completed
          </button>
          <button @click="filterStatus = 'Short-Pick'" :class="filterStatus === 'Short-Pick' ? 'bg-orange-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-orange-500 hover:text-white">
            Short-Pick
          </button>
        </div>
      </div>
    </div>

    <!-- Tabel Picking Tasks -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow overflow-hidden">
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
          <thead class="bg-gray-50 dark:bg-gray-700">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">TO Number</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No Reservasi</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Tanggal Dibuat</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Requester / Departemen</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Status</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Aksi</th>
            </tr>
          </thead>
          <tbody class="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
            <tr v-for="task in filteredTasks" :key="task.id" class="hover:bg-gray-50 dark:hover:bg-gray-700">
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">{{ task.toNumber }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-blue-600 dark:text-blue-400 hover:underline cursor-pointer" @click="viewReservation(task.noReservasi)">
                {{ task.noReservasi }}
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ formatDateTime(task.tanggalDibuat) }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ task.requester }} / {{ task.departemen }}</td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span :class="getStatusClass(task.status)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                  {{ task.status }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                <div class="flex space-x-2">
                  <button @click="viewDetail(task)" class="bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300 hover:bg-blue-200 dark:hover:bg-blue-800 px-2 py-1 rounded text-xs">
                    Detail
                  </button>
                  <button v-if="task.status === 'Pending' || task.status === 'In Progress'" @click="startPicking(task)" class="bg-green-100 dark:bg-green-900 text-green-700 dark:text-green-300 hover:bg-green-200 dark:hover:bg-green-800 px-2 py-1 rounded text-xs">
                    Kerjakan
                  </button>
                  <button @click="printPickingList(task)" class="bg-purple-100 dark:bg-purple-900 text-purple-700 dark:text-purple-300 hover:bg-purple-200 dark:hover:bg-purple-800 px-2 py-1 rounded text-xs">
                    Cetak Picking List
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Modal Detail Picking Task -->
    <div v-if="showPickingModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-7xl w-full mx-4 max-h-[90vh] overflow-y-auto">
        <div class="p-6">
          <!-- Header Modal -->
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Detail Picking Task - {{ selectedTask?.toNumber }}</h3>
            <button @click="closePickingModal" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>

          <!-- Header Info -->
          <div v-if="selectedTask" class="bg-gray-50 dark:bg-gray-700 rounded-lg p-4 mb-6">
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
              <div>
                <span class="text-sm font-medium text-gray-700 dark:text-gray-300">TO Number:</span>
                <div class="text-gray-900 dark:text-white font-medium">{{ selectedTask.toNumber }}</div>
              </div>
              <div>
                <span class="text-sm font-medium text-gray-700 dark:text-gray-300">No Reservasi:</span>
                <div class="text-blue-600 dark:text-blue-400 cursor-pointer hover:underline" @click="viewReservation(selectedTask.noReservasi)">{{ selectedTask.noReservasi }}</div>
              </div>
              <div>
                <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Tanggal Dibuat:</span>
                <div class="text-gray-900 dark:text-white">{{ formatDateTime(selectedTask.tanggalDibuat) }}</div>
              </div>
              <div>
                <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Requester:</span>
                <div class="text-gray-900 dark:text-white">{{ selectedTask.requester }} / {{ selectedTask.departemen }}</div>
              </div>
            </div>
          </div>

          <!-- QR Scanner Section -->
          <div class="bg-blue-50 dark:bg-blue-900 rounded-lg p-4 mb-6">
            <div class="flex items-center justify-between">
              <div class="flex items-center space-x-3">
                <svg class="w-6 h-6 text-blue-600 dark:text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 20h4M4 12h4m12 0h2M4 4h16a2 2 0 012 2v12a2 2 0 01-2 2H4a2 2 0 01-2-2V6a2 2 0 012-2z"/>
                </svg>
                <div>
                  <div class="text-sm font-medium text-blue-900 dark:text-blue-200">QR Scanner</div>
                  <div class="text-xs text-blue-700 dark:text-blue-300">Scan QR Box → QR Source Bin → Input Qty → Scan Dest Bin (opsional)</div>
                </div>
              </div>
              <button @click="openQRScanner" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-md text-sm">
                Scan QR
              </button>
            </div>
          </div>

          <!-- Items Table -->
          <div class="border border-gray-200 dark:border-gray-600 rounded-lg overflow-hidden">
            <div class="overflow-x-auto">
              <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-600" style="min-width: 1200px;">
                <thead class="bg-gray-50 dark:bg-gray-700">
                  <tr>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">No</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Kode Item</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Nama Material</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Lot/Serial</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Source Bin</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Dest Bin</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Qty Diminta</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Qty Picked</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">UoM</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Status</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200 dark:divide-gray-600 bg-white dark:bg-gray-800">
                  <tr v-for="(item, index) in selectedTask?.items || []" :key="index" :class="item.status === 'Picked' ? 'bg-green-50 dark:bg-green-900' : item.status === 'Short-Pick' ? 'bg-orange-50 dark:bg-orange-900' : ''">
                    <td class="px-3 py-2 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ index + 1 }}</td>
                    <td class="px-3 py-2 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">{{ item.kodeItem }}</td>
                    <td class="px-3 py-2 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.namaMaterial }}</td>
                    <td class="px-3 py-2 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.lotSerial }}</td>
                    <td class="px-3 py-2 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.sourceBin }}</td>
                    <td class="px-3 py-2 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.destBin }}</td>
                    <td class="px-3 py-2 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.qtyDiminta }}</td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <input v-model="item.qtyPicked" type="number" :max="item.qtyDiminta" class="w-20 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white" @input="updateItemStatus(item)">
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.uom }}</td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <span :class="getItemStatusClass(item.status)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                        {{ item.status }}
                      </span>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <!-- Footer Modal -->
          <div class="flex justify-between items-center mt-6 pt-6 border-t border-gray-200 dark:border-gray-600">
            <div class="text-sm text-gray-600 dark:text-gray-400">
              Progress: {{ getPickedItemsCount }}/{{ selectedTask?.items?.length || 0 }} items picked
            </div>
            <div class="flex space-x-3">
              <button @click="closePickingModal" class="px-4 py-2 text-gray-700 dark:text-gray-300 bg-gray-200 dark:bg-gray-600 rounded-md hover:bg-gray-300 dark:hover:bg-gray-500">
                Batal
              </button>
              <button @click="finishPicking" :disabled="!canFinishPicking" class="px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 disabled:bg-gray-400">
                Selesai Picking
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal QR Scanner -->
    <div v-if="showQRScannerModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-md w-full mx-4">
        <div class="p-6">
          <div class="flex justify-between items-center mb-4">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">QR Scanner - Picking</h3>
            <button @click="closeQRScanner" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>
          
          <div class="space-y-4">
            <!-- Current Scan Step -->
            <div class="bg-blue-50 dark:bg-blue-900 p-3 rounded-lg">
              <div class="text-sm font-medium text-blue-900 dark:text-blue-200">Step {{ scanStep }}/4</div>
              <div class="text-xs text-blue-700 dark:text-blue-300">{{ getScanStepText }}</div>
            </div>

            <div class="bg-gray-100 dark:bg-gray-700 p-4 rounded-lg text-center">
              <svg class="w-16 h-16 mx-auto text-gray-400 mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 20h4M4 12h4m12 0h2M4 4h16a2 2 0 012 2v12a2 2 0 01-2-2V6a2 2 0 012-2z"/>
              </svg>
              <p class="text-sm text-gray-600 dark:text-gray-400">Arahkan kamera ke QR Code</p>
            </div>
            
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">Atau input manual QR Code:</label>
              <input v-model="qrInput" @keyup.enter="processQR" type="text" placeholder="Scan atau ketik QR Code..." class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>
            
            <!-- Manual Qty Input (when scanning box) -->
            <div v-if="scanStep === 3" class="space-y-2">
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">Qty yang Dipick:</label>
              <input v-model="manualQty" type="number" :max="currentScanItem?.qtyDiminta" placeholder="Masukkan quantity..." class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>
            
            <div class="flex space-x-3">
              <button @click="processQR" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-2 px-4 rounded-md">
                {{ scanStep === 3 ? 'Confirm Qty' : 'Process QR' }}
              </button>
              <button v-if="scanStep > 1" @click="prevScanStep" class="bg-gray-500 hover:bg-gray-600 text-white py-2 px-4 rounded-md">
                Back
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// Data reaktif
const filterStatus = ref('ALL')
const pickingTasks = ref([])
const showPickingModal = ref(false)
const showQRScannerModal = ref(false)
const selectedTask = ref(null)
const qrInput = ref('')
const scanStep = ref(1)
const currentScanItem = ref(null)
const manualQty = ref('')

// Initialize dummy data
const initDummyData = () => {
  pickingTasks.value = [
    {
      id: 1,
      toNumber: 'TO/20250918/0001',
      noReservasi: 'RSV/20250918/0001',
      tanggalDibuat: '2025-09-18T08:30:00',
      requester: 'John Doe',
      departemen: 'FOH',
      status: 'Pending',
      items: [
        {
          kodeItem: 'FOH-001',
          namaMaterial: 'Peralatan Makan',
          lotSerial: 'LOT001',
          sourceBin: 'A-01-01',
          destBin: 'STAGING-001',
          qtyDiminta: 50,
          qtyPicked: 0,
          uom: 'PCS',
          status: 'Pending'
        },
        {
          kodeItem: 'FOH-002',
          namaMaterial: 'Gelas Plastik',
          lotSerial: 'LOT002',
          sourceBin: 'A-01-02',
          destBin: 'STAGING-001',
          qtyDiminta: 100,
          qtyPicked: 0,
          uom: 'PCS',
          status: 'Pending'
        }
      ]
    },
    {
      id: 2,
      toNumber: 'TO/20250918/0002',
      noReservasi: 'RSV/20250918/0002',
      tanggalDibuat: '2025-09-18T10:15:00',
      requester: 'Jane Smith',
      departemen: 'Production',
      status: 'In Progress',
      items: [
        {
          kodeItem: 'PM-001',
          namaMaterial: 'Botol Plastik',
          lotSerial: 'BATCH001',
          sourceBin: 'B-02-01',
          destBin: 'PROD-LINE-A',
          qtyDiminta: 1000,
          qtyPicked: 750,
          uom: 'PCS',
          status: 'Short-Pick'
        }
      ]
    },
    {
      id: 3,
      toNumber: 'TO/20250918/0003',
      noReservasi: 'RSV/20250917/0003',
      tanggalDibuat: '2025-09-17T14:20:00',
      requester: 'Bob Wilson',
      departemen: 'Kitchen',
      status: 'Completed',
      items: [
        {
          kodeItem: 'RM-001',
          namaMaterial: 'Tepung Terigu',
          lotSerial: 'BATCH002',
          sourceBin: 'C-03-01',
          destBin: 'KITCHEN-A',
          qtyDiminta: 25,
          qtyPicked: 25,
          uom: 'KG',
          status: 'Picked'
        }
      ]
    }
  ]
}

// Computed properties
const filteredTasks = computed(() => {
  if (filterStatus.value === 'ALL') {
    return pickingTasks.value
  }
  return pickingTasks.value.filter(task => task.status === filterStatus.value)
})

const getPickedItemsCount = computed(() => {
  if (!selectedTask.value?.items) return 0
  return selectedTask.value.items.filter(item => item.status === 'Picked' || item.status === 'Short-Pick').length
})

const canFinishPicking = computed(() => {
  if (!selectedTask.value?.items) return false
  return selectedTask.value.items.every(item => item.qtyPicked > 0)
})

const getScanStepText = computed(() => {
  const steps = [
    '',
    'Scan QR Box untuk validasi item',
    'Scan QR Source Bin untuk validasi lokasi',
    'Input quantity yang dipick',
    'Scan QR Dest Bin (opsional)'
  ]
  return steps[scanStep.value]
})

// Methods
const getStatusClass = (status) => {
  const classes = {
    'Pending': 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300',
    'In Progress': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
    'Completed': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300',
    'Short-Pick': 'bg-orange-100 text-orange-800 dark:bg-orange-900 dark:text-orange-300'
  }
  return classes[status] || 'bg-gray-100 text-gray-800'
}

const getItemStatusClass = (status) => {
  const classes = {
    'Pending': 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300',
    'Picked': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300',
    'Short-Pick': 'bg-orange-100 text-orange-800 dark:bg-orange-900 dark:text-orange-300'
  }
  return classes[status] || 'bg-gray-100 text-gray-800'
}

const formatDateTime = (dateString) => {
  return new Date(dateString).toLocaleString('id-ID', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit'
  })
}

const viewReservation = (noReservasi) => {
  alert(`Melihat detail reservasi: ${noReservasi}`)
}

const viewDetail = (task) => {
  alert(`Melihat detail picking task: ${task.toNumber}`)
}

const startPicking = (task) => {
  selectedTask.value = task
  if (task.status === 'Pending') {
    // Update status to In Progress
    const taskIndex = pickingTasks.value.findIndex(t => t.id === task.id)
    if (taskIndex !== -1) {
      pickingTasks.value[taskIndex].status = 'In Progress'
    }
  }
  showPickingModal.value = true
}

const closePickingModal = () => {
  showPickingModal.value = false
  selectedTask.value = null
  resetScanner()
}

const openQRScanner = () => {
  showQRScannerModal.value = true
  resetScanner()
}

const closeQRScanner = () => {
  showQRScannerModal.value = false
  resetScanner()
}

const resetScanner = () => {
  scanStep.value = 1
  qrInput.value = ''
  currentScanItem.value = null
  manualQty.value = ''
}

const processQR = () => {
  if (!qrInput.value && scanStep.value !== 3) {
    alert('Silakan scan atau input QR Code!')
    return
  }

  switch (scanStep.value) {
    case 1: // Scan QR Box
      validateQRBox(qrInput.value)
      break
    case 2: // Scan QR Source Bin
      validateSourceBin(qrInput.value)
      break
    case 3: // Input Qty
      confirmQuantity()
      break
    case 4: // Scan QR Dest Bin
      validateDestBin(qrInput.value)
      break
  }
}

const validateQRBox = (qrContent) => {
  // Parse QR: shipment_id|item_id|lot_no|qty|exp_date|status
  const qrParts = qrContent.split('|')
  if (qrParts.length < 2) {
    alert('Format QR Box tidak valid!')
    return
  }
  
  const itemCode = qrParts[1]
  const lotNo = qrParts[2]
  
  // Find matching item in picking list
  const matchingItem = selectedTask.value.items.find(item => 
    item.kodeItem === itemCode && item.lotSerial === lotNo && item.status === 'Pending'
  )
  
  if (!matchingItem) {
    alert('Item tidak sesuai dengan picking list atau sudah dipick!')
    return
  }
  
  currentScanItem.value = matchingItem
  scanStep.value = 2
  qrInput.value = ''
  alert(`✓ Item valid: ${matchingItem.kodeItem} - ${matchingItem.namaMaterial}`)
}

const validateSourceBin = (binCode) => {
  if (!currentScanItem.value) return
  
  if (binCode !== currentScanItem.value.sourceBin) {
    alert(`Lokasi bin salah!\nHarus: ${currentScanItem.value.sourceBin}\nDi-scan: ${binCode}`)
    return
  }
  
  scanStep.value = 3
  qrInput.value = ''
  manualQty.value = currentScanItem.value.qtyDiminta.toString()
  alert(`✓ Source Bin valid: ${binCode}`)
}

const confirmQuantity = () => {
  if (!manualQty.value || parseInt(manualQty.value) <= 0) {
    alert('Silakan input quantity yang valid!')
    return
  }
  
  const qtyPicked = parseInt(manualQty.value)
  const qtyDiminta = currentScanItem.value.qtyDiminta
  
  if (qtyPicked > qtyDiminta) {
    alert(`Quantity melebihi yang diminta!\nDiminta: ${qtyDiminta}\nInput: ${qtyPicked}`)
    return
  }
  
  // Update item
  currentScanItem.value.qtyPicked = qtyPicked
  
  if (qtyPicked === qtyDiminta) {
    currentScanItem.value.status = 'Picked'
    alert(`✓ Item berhasil dipick complete: ${qtyPicked}/${qtyDiminta}`)
  } else {
    currentScanItem.value.status = 'Short-Pick'
    alert(`⚠ Short-pick detected: ${qtyPicked}/${qtyDiminta}`)
  }
  
  // Check if has dest bin
  if (currentScanItem.value.destBin && currentScanItem.value.destBin !== '-') {
    scanStep.value = 4
    qrInput.value = ''
  } else {
    // No dest bin, finish this item
    finishCurrentItem()
  }
}

const validateDestBin = (binCode) => {
  if (!currentScanItem.value) return
  
  if (binCode !== currentScanItem.value.destBin) {
    alert(`Destination bin salah!\nHarus: ${currentScanItem.value.destBin}\nDi-scan: ${binCode}`)
    return
  }
  
  alert(`✓ Destination Bin valid: ${binCode}`)
  finishCurrentItem()
}

const finishCurrentItem = () => {
  alert(`Item ${currentScanItem.value.kodeItem} berhasil diselesaikan!`)
  resetScanner()
  
  // Check if there are more items to pick
  const remainingItems = selectedTask.value.items.filter(item => item.status === 'Pending')
  if (remainingItems.length === 0) {
    closeQRScanner()
    alert('Semua item sudah dipick! Silakan klik "Selesai Picking"')
  }
}

const prevScanStep = () => {
  if (scanStep.value > 1) {
    scanStep.value--
    qrInput.value = ''
    if (scanStep.value === 1) {
      currentScanItem.value = null
    }
  }
}

const updateItemStatus = (item) => {
  if (!item.qtyPicked || item.qtyPicked <= 0) {
    item.status = 'Pending'
    return
  }
  
  if (item.qtyPicked >= item.qtyDiminta) {
    item.status = 'Picked'
    item.qtyPicked = item.qtyDiminta // Cap at max
  } else {
    item.status = 'Short-Pick'
  }
}

const finishPicking = () => {
  if (!selectedTask.value) return
  
  const allItems = selectedTask.value.items
  const pickedItems = allItems.filter(item => item.status === 'Picked')
  const shortPickItems = allItems.filter(item => item.status === 'Short-Pick')
  
  let newStatus = 'Completed'
  let message = `Picking selesai!\n\nTotal items: ${allItems.length}\nPicked: ${pickedItems.length}`
  
  if (shortPickItems.length > 0) {
    newStatus = 'Short-Pick'
    message += `\nShort-pick: ${shortPickItems.length}`
    message += '\n\nSistem akan:\n- Update stok\n- Trigger reallocation untuk shortage\n- Notifikasi supervisor'
  } else {
    message += '\n\nSistem akan:\n- Update stok dari Source Bin\n- Pindahkan ke Destination Bin\n- Simpan transaksi picking'
  }
  
  // Update task status
  const taskIndex = pickingTasks.value.findIndex(t => t.id === selectedTask.value.id)
  if (taskIndex !== -1) {
    pickingTasks.value[taskIndex].status = newStatus
  }
  
  alert(message)
  closePickingModal()
}

const printPickingList = (task) => {
  // Create Picking Slip PDF
  const printWindow = window.open('', '_blank')
  
  const totalItems = task.items.length
  const pickedItems = task.items.filter(item => item.status === 'Picked').length
  const shortPickItems = task.items.filter(item => item.status === 'Short-Pick').length
  
  printWindow.document.write(`
    <html>
      <head>
        <title>Picking List - ${task.toNumber}</title>
        <style>
          body { font-family: Arial, sans-serif; margin: 20px; font-size: 12px; }
          .header { text-align: center; border-bottom: 2px solid #000; padding-bottom: 15px; margin-bottom: 20px; }
          .info-section { margin: 15px 0; }
          .info-row { display: flex; justify-content: space-between; margin: 8px 0; }
          .items-table { width: 100%; border-collapse: collapse; margin: 20px 0; }
          .items-table th, .items-table td { border: 1px solid #000; padding: 6px; text-align: left; font-size: 11px; }
          .items-table th { background-color: #f0f0f0; font-weight: bold; }
          .picked { background-color: #e6ffe6; }
          .short-pick { background-color: #fff2e6; }
          .summary { margin: 20px 0; padding: 10px; background-color: #f9f9f9; border: 1px solid #ddd; }
          .signature { margin-top: 40px; display: flex; justify-content: space-between; }
          .signature div { text-align: center; }
          .signature-line { border-top: 1px solid #000; width: 150px; margin-top: 40px; }
          @media print {
            body { margin: 0; font-size: 10px; }
          }
        </style>
      </head>
      <body>
        <div class="header">
          <h2>PICKING LIST</h2>
          <p><strong>TO Number: ${task.toNumber}</strong></p>
          <p>Creation Date: ${formatDateTime(task.tanggalDibuat)} | Warehouse: Main WH</p>
        </div>
        
        <div class="info-section">
          <div class="info-row">
            <span><strong>No Reservasi:</strong> ${task.noReservasi}</span>
            <span><strong>Transaction Type:</strong> Picking</span>
          </div>
          <div class="info-row">
            <span><strong>Requester:</strong> ${task.requester}</span>
            <span><strong>Departemen:</strong> ${task.departemen}</span>
          </div>
          <div class="info-row">
            <span><strong>Status:</strong> ${task.status}</span>
            <span><strong>Print Date:</strong> ${new Date().toLocaleString('id-ID')}</span>
          </div>
        </div>

        <div class="summary">
          <strong>Summary:</strong> Total ${totalItems} items | Picked: ${pickedItems} | Short-pick: ${shortPickItems} | Pending: ${totalItems - pickedItems - shortPickItems}
        </div>
        
        <table class="items-table">
          <tr>
            <th>No</th>
            <th>Kode Item</th>
            <th>Deskripsi</th>
            <th>Lot/Serial</th>
            <th>Source Bin</th>
            <th>Dest Bin</th>
            <th>Qty Diminta</th>
            <th>Qty Picked</th>
            <th>UoM</th>
            <th>Status</th>
          </tr>
          ${task.items.map((item, index) => `
            <tr class="${item.status === 'Picked' ? 'picked' : item.status === 'Short-Pick' ? 'short-pick' : ''}">
              <td>${index + 1}</td>
              <td>${item.kodeItem}</td>
              <td>${item.namaMaterial}</td>
              <td>${item.lotSerial}</td>
              <td>${item.sourceBin}</td>
              <td>${item.destBin}</td>
              <td>${item.qtyDiminta}</td>
              <td>${item.qtyPicked || '-'}</td>
              <td>${item.uom}</td>
              <td>${item.status}</td>
            </tr>
          `).join('')}
        </table>
        
        <div class="signature">
          <div>
            <p>Diserahkan Oleh:</p>
            <div class="signature-line"></div>
            <p>Warehouse Staff</p>
          </div>
          <div>
            <p>Diterima Oleh:</p>
            <div class="signature-line"></div>
            <p>${task.requester}</p>
          </div>
          <div>
            <p>Mengetahui:</p>
            <div class="signature-line"></div>
            <p>Supervisor</p>
          </div>
        </div>
      </body>
    </html>
  `)
  
  printWindow.document.close()
  printWindow.focus()
  
  setTimeout(() => {
    printWindow.print()
    printWindow.close()
  }, 500)
}

// Lifecycle
onMounted(() => {
  initDummyData()
})
</script>