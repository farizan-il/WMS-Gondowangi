<template>
  <div class="min-h-screen bg-gray-50 p-6">
    <!-- Header -->
    <div class="mb-6">
      <h1 class="text-2xl font-bold text-gray-900 mb-2">Putaway & Transfer Order</h1>
      <p class="text-gray-600">Kelola Transfer Order untuk putaway, transfer, dan picking barang</p>
    </div>

    <!-- Action Buttons -->
    <!-- <div class="mb-6 flex gap-4">
      <button
        @click="showCreateModal = true"
        class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 font-medium"
      >
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6"></path>
        </svg>
        Buat TO Manual
      </button>
      <button
        @click="generateAutoPutaway"
        class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 font-medium"
      >
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
        </svg>
        Generate Auto Putaway
      </button>
    </div> -->

    <!-- Filter & Search -->
    <div class="bg-white rounded-lg shadow-sm p-4 mb-6">
      <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Search TO Number</label>
          <input
            v-model="searchQuery"
            type="text"
            placeholder="TO-2024-001..."
            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Tipe</label>
          <select
            v-model="filterType"
            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
            <option value="">Semua Tipe</option>
            <option value="Putaway">Putaway</option>
            <option value="Transfer">Transfer</option>
            <option value="Picking">Picking</option>
          </select>
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Status</label>
          <select
            v-model="filterStatus"
            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
            <option value="">Semua Status</option>
            <option value="Pending">Pending</option>
            <option value="In Progress">In Progress</option>
            <option value="Completed">Completed</option>
          </select>
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700 mb-2">Warehouse</label>
          <select
            v-model="filterWarehouse"
            class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
            <option value="">Semua Gudang</option>
            <option value="WH-001">WH-001 - Gudang Utama</option>
            <option value="WH-002">WH-002 - Gudang Karantina</option>
          </select>
        </div>
      </div>
    </div>

    <!-- Transfer Orders Table -->
    <div class="bg-white rounded-lg shadow-sm overflow-hidden">
      <div class="px-6 py-4 border-b border-gray-200">
        <h2 class="text-lg font-semibold text-gray-900">Daftar Transfer Order</h2>
        <p class="text-sm text-gray-600 mt-1">Total: {{ filteredTransferOrders.length }} TO</p>
      </div>
      
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-50">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">TO Number</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Creation Date</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Warehouse</th>
              <!-- <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Tipe</th> -->
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Items</th>
              <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
            </tr>
          </thead>
          <tbody class="bg-white divide-y divide-gray-200">
            <tr v-for="to in filteredTransferOrders" :key="to.id" class="hover:bg-gray-50">
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="font-medium text-gray-900">{{ to.toNumber }}</div>
                <div v-if="to.reservationNo" class="text-sm text-gray-500">{{ to.reservationNo }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                {{ formatDate(to.creationDate) }}
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                {{ to.warehouse }}
              </td>
              <!-- <td class="px-6 py-4 whitespace-nowrap">
                <span :class="getTypeClass(to.type)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                  {{ to.type }}
                </span>
              </td> -->
              <td class="px-6 py-4 whitespace-nowrap">
                <span :class="getStatusClass(to.status)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                  {{ to.status }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                {{ to.items.length }} item(s)
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                <div class="flex items-center justify-end gap-2">
                  <button
                    @click="viewDetail(to)"
                    class="text-blue-600 hover:text-blue-900 p-1 rounded"
                    title="Detail"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"></path>
                    </svg>
                  </button>
                  <button
                    v-if="to.status !== 'Completed'"
                    @click="executeTO(to)"
                    class="text-green-600 hover:text-green-900 p-1 rounded"
                    title="Kerjakan"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.828 14.828a4 4 0 01-5.656 0M9 10h1.586a1 1 0 01.707.293l2.414 2.414a1 1 0 00.707.293H15M6 6h3m5 0h3"></path>
                    </svg>
                  </button>
                  <button
                    @click="printTO(to)"
                    class="text-purple-600 hover:text-purple-900 p-1 rounded"
                    title="Cetak TO"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z"></path>
                    </svg>
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Create TO Modal -->
    <div v-if="showCreateModal" class="fixed inset-0 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: rgba(43, 51, 63, 0.67);">
      <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-semibold">Buat Transfer Order Manual</h3>
          <button @click="showCreateModal = false" class="text-gray-400 hover:text-gray-600">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
        </div>
        
        <form @submit.prevent="createManualTO">
          <div class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Tipe Transfer</label>
              <select v-model="newTO.type" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                <option value="Transfer">Transfer</option>
                <option value="Picking">Picking</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Warehouse</label>
              <select v-model="newTO.warehouse" required class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
                <option value="WH-001">WH-001 - Gudang Utama</option>
                <option value="WH-002">WH-002 - Gudang Karantina</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">No Reservasi (Optional)</label>
              <input v-model="newTO.reservationNo" type="text" placeholder="RSV-2024-001" class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500">
            </div>
          </div>
          
          <div class="flex gap-3 mt-6">
            <button type="button" @click="showCreateModal = false" class="flex-1 px-4 py-2 border border-gray-300 text-gray-700 rounded-md hover:bg-gray-50">
              Batal
            </button>
            <button type="submit" class="flex-1 px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">
              Buat TO
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Detail/Execute TO Modal -->
    <div v-if="showDetailModal" class="fixed inset-0 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: rgba(43, 51, 63, 0.67);">
      <div class="bg-white rounded-lg p-6 w-full max-w-7xl max-h-screen overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
          <div>
            <h3 class="text-xl font-semibold">{{ selectedTO.isExecuting ? 'Kerjakan' : 'Detail' }} Transfer Order</h3>
            <p class="text-gray-600 mt-1">{{ selectedTO.toNumber }}</p>
          </div>
          <button @click="closeDetailModal" class="text-gray-400 hover:text-gray-600">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
        </div>

        <!-- TO Info -->
        <div class="bg-gray-50 rounded-lg p-4 mb-6">
          <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
            <div>
              <label class="block text-sm font-medium text-gray-700">TO Number</label>
              <p class="text-sm text-gray-900 mt-1">{{ selectedTO.toNumber }}</p>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700">Transaction Type</label>
              <p class="text-sm text-gray-900 mt-1">{{ selectedTO.type }}</p>
            </div>
            <div v-if="selectedTO.reservationNo">
              <label class="block text-sm font-medium text-gray-700">No Reservasi</label>
              <p class="text-sm text-gray-900 mt-1">{{ selectedTO.reservationNo }}</p>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700">Status</label>
              <span :class="getStatusClass(selectedTO.status)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full mt-1">
                {{ selectedTO.status }}
              </span>
            </div>
          </div>
        </div>

        <!-- Items Table -->
        <div class="mb-6">
          <h4 class="text-lg font-medium text-gray-900 mb-4">Daftar Item</h4>
          <div class="overflow-x-auto">
            <table class="w-full border border-gray-200 rounded-lg">
              <thead class="bg-gray-50">
                <tr>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">No</th>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Kode Item</th>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Nama Material</th>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Source Bin</th>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Dest Bin</th>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Qty</th>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">UoM</th>
                  <th class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Status</th>
                  <th v-if="selectedTO.isExecuting" class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase">Actions</th>
                </tr>
              </thead>
              <tbody class="bg-white divide-y divide-gray-200">
                <tr v-for="(item, index) in selectedTO.items" :key="index" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900">{{ index + 1 }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ item.itemCode }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ item.materialName }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ item.sourceBin }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ item.destBin }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">
                    <div v-if="selectedTO.isExecuting && item.status !== 'completed'">
                      <input
                        v-model="item.actualQty"
                        type="number"
                        :placeholder="item.qty.toString()"
                        class="w-20 px-2 py-1 border border-gray-300 rounded text-center"
                        min="0"
                      >
                    </div>
                    <div v-else>{{ item.actualQty || item.qty }}</div>
                  </td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ item.uom }}</td>
                  <td class="px-4 py-3">
                    <span :class="getItemStatusClass(item.status)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                      {{ item.status }}
                    </span>
                  </td>
                  <td v-if="selectedTO.isExecuting" class="px-4 py-3">
                    <div v-if="item.status !== 'completed'" class="flex gap-2">
                      <button
                        @click="scanBox(item)"
                        :class="item.boxScanned ? 'bg-green-100 text-green-800' : 'bg-blue-100 text-blue-800'"
                        class="px-2 py-1 text-xs font-medium rounded"
                      >
                        {{ item.boxScanned ? '✓ Box' : 'Scan Box' }}
                      </button>
                      <button
                        @click="scanSourceBin(item)"
                        :class="item.sourceBinScanned ? 'bg-green-100 text-green-800' : 'bg-blue-100 text-blue-800'"
                        class="px-2 py-1 text-xs font-medium rounded"
                      >
                        {{ item.sourceBinScanned ? '✓ Source' : 'Scan Source' }}
                      </button>
                      <button
                        @click="scanDestBin(item)"
                        :class="item.destBinScanned ? 'bg-green-100 text-green-800' : 'bg-blue-100 text-blue-800'"
                        class="px-2 py-1 text-xs font-medium rounded"
                      >
                        {{ item.destBinScanned ? '✓ Dest' : 'Scan Dest' }}
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Actions -->
        <div class="flex gap-3 justify-end">
          <button @click="closeDetailModal" class="px-4 py-2 border border-gray-300 text-gray-700 rounded-md hover:bg-gray-50">
            {{ selectedTO.isExecuting ? 'Batal' : 'Tutup' }}
          </button>
          <button
            v-if="selectedTO.isExecuting"
            @click="completeTO"
            :disabled="!canCompleteTO"
            :class="canCompleteTO ? 'bg-green-600 hover:bg-green-700' : 'bg-gray-400 cursor-not-allowed'"
            class="px-4 py-2 text-white rounded-md font-medium"
          >
            Selesai TO
          </button>
        </div>
      </div>
    </div>

    <!-- QR Scanner Modal -->
    <div v-if="showQRModal" class="fixed inset-0 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: rgba(43, 51, 63, 0.67);">
      <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-semibold">{{ qrScanType }} Scanner</h3>
          <button @click="closeQRModal" class="text-gray-400 hover:text-gray-600">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
        </div>

        <div class="text-center">
          <div class="w-48 h-48 bg-gray-100 border-2 border-dashed border-gray-300 rounded-lg mx-auto flex items-center justify-center mb-4">
            <div class="text-center">
              <svg class="w-12 h-12 text-gray-400 mx-auto mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 16h4m-4 0h2m-6-4h2m-8-2h.01M8 12h2m-2 0V8m0 0h2m-2 0H4m0 0h4v4m0-4h2"></path>
              </svg>
              <p class="text-gray-500">QR Scanner Area</p>
            </div>
          </div>
          
          <div class="space-y-4">
            <input
              v-model="qrInput"
              type="text"
              :placeholder="`Input ${qrScanType} Code Manual`"
              class="w-full px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
            
            <div class="flex gap-3">
              <button @click="closeQRModal" class="flex-1 px-4 py-2 border border-gray-300 text-gray-700 rounded-md hover:bg-gray-50">
                Batal
              </button>
              <button @click="confirmQRScan" class="flex-1 px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">
                Confirm Scan
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

// Interfaces
interface TOItem {
  itemCode: string
  materialName: string
  sourceBin: string
  destBin: string
  qty: number
  actualQty?: number
  uom: string
  status: 'pending' | 'in_progress' | 'completed'
  boxScanned: boolean
  sourceBinScanned: boolean
  destBinScanned: boolean
}

interface TransferOrder {
  id: string
  toNumber: string
  creationDate: Date
  warehouse: string
  type: 'Putaway' | 'Transfer' | 'Picking'
  status: 'Pending' | 'In Progress' | 'Completed'
  reservationNo?: string
  items: TOItem[]
  isExecuting?: boolean
}

// Reactive data
const transferOrders = ref<TransferOrder[]>([])
const searchQuery = ref('')
const filterType = ref('')
const filterStatus = ref('')
const filterWarehouse = ref('')

// Modals
const showCreateModal = ref(false)
const showDetailModal = ref(false)
const showQRModal = ref(false)

// Selected data
const selectedTO = ref<TransferOrder | null>(null)
const currentItem = ref<TOItem | null>(null)
const qrScanType = ref('')
const qrInput = ref('')

// Form data
const newTO = ref({
  type: 'Transfer',
  warehouse: 'WH-001',
  reservationNo: ''
})

// Computed
const filteredTransferOrders = computed(() => {
  return transferOrders.value.filter(to => {
    const matchesSearch = !searchQuery.value || to.toNumber.toLowerCase().includes(searchQuery.value.toLowerCase())
    const matchesType = !filterType.value || to.type === filterType.value
    const matchesStatus = !filterStatus.value || to.status === filterStatus.value
    const matchesWarehouse = !filterWarehouse.value || to.warehouse === filterWarehouse.value
    
    return matchesSearch && matchesType && matchesStatus && matchesWarehouse
  })
})

const canCompleteTO = computed(() => {
  if (!selectedTO.value) return false
  return selectedTO.value.items.every(item => 
    item.boxScanned && item.sourceBinScanned && item.destBinScanned && 
    (item.actualQty !== undefined || item.qty > 0)
  )
})

// Methods
const generateTONumber = () => {
  const date = new Date()
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const sequence = String(transferOrders.value.length + 1).padStart(3, '0')
  return `TO-${year}-${month}-${sequence}`
}

const createManualTO = () => {
  const newTransferOrder: TransferOrder = {
    id: Date.now().toString(),
    toNumber: generateTONumber(),
    creationDate: new Date(),
    warehouse: newTO.value.warehouse,
    type: newTO.value.type as 'Transfer' | 'Picking',
    status: 'Pending',
    reservationNo: newTO.value.reservationNo || undefined,
    items: [
      {
        itemCode: 'CHM-001',
        materialName: 'Chemical A - Released from Quarantine',
        sourceBin: 'QTN-A-01',
        destBin: 'STD-A-02-01',
        qty: 25,
        uom: 'KG',
        status: 'pending',
        boxScanned: false,
        sourceBinScanned: false,
        destBinScanned: false
      },
      {
        itemCode: 'CHM-002',
        materialName: 'Chemical B - Released from Quarantine',
        sourceBin: 'QTN-A-02',
        destBin: 'STD-B-01-03',
        qty: 15,
        uom: 'L',
        status: 'pending',
        boxScanned: false,
        sourceBinScanned: false,
        destBinScanned: false
      }
    ]
  }

  transferOrders.value.unshift(autoPutaway)
  
  // Notification
  alert('Auto Putaway TO berhasil di-generate!')
}

const viewDetail = (to: TransferOrder) => {
  selectedTO.value = { ...to, isExecuting: false }
  showDetailModal.value = true
}

const executeTO = (to: TransferOrder) => {
  selectedTO.value = { 
    ...to, 
    isExecuting: true,
    status: 'In Progress',
    items: to.items.map(item => ({ ...item, status: 'in_progress' as const }))
  }
  
  // Update original TO status
  const originalIndex = transferOrders.value.findIndex(t => t.id === to.id)
  if (originalIndex !== -1) {
    transferOrders.value[originalIndex].status = 'In Progress'
  }
  
  showDetailModal.value = true
}

const closeDetailModal = () => {
  selectedTO.value = null
  showDetailModal.value = false
}

const scanBox = (item: TOItem) => {
  currentItem.value = item
  qrScanType.value = 'Box QR'
  qrInput.value = ''
  showQRModal.value = true
}

const scanSourceBin = (item: TOItem) => {
  currentItem.value = item
  qrScanType.value = 'Source Bin'
  qrInput.value = ''
  showQRModal.value = true
}

const scanDestBin = (item: TOItem) => {
  currentItem.value = item
  qrScanType.value = 'Destination Bin'
  qrInput.value = ''
  showQRModal.value = true
}

const closeQRModal = () => {
  showQRModal.value = false
  currentItem.value = null
  qrScanType.value = ''
  qrInput.value = ''
}

const confirmQRScan = () => {
  if (!currentItem.value || !qrInput.value.trim()) {
    alert('Please input QR code!')
    return
  }

  const item = currentItem.value

  switch (qrScanType.value) {
    case 'Box QR':
      // Validasi box QR
      if (qrInput.value.includes(item.itemCode)) {
        item.boxScanned = true
        alert('Box QR berhasil di-scan!')
      } else {
        alert('Box QR tidak sesuai dengan item!')
        return
      }
      break
      
    case 'Source Bin':
      // Validasi source bin
      if (qrInput.value === item.sourceBin) {
        item.sourceBinScanned = true
        alert('Source Bin berhasil di-scan!')
      } else {
        alert(`Source Bin tidak sesuai! Expected: ${item.sourceBin}`)
        return
      }
      break
      
    case 'Destination Bin':
      // Validasi destination bin
      if (qrInput.value === item.destBin) {
        item.destBinScanned = true
        alert('Destination Bin berhasil di-scan!')
      } else {
        alert(`Destination Bin tidak sesuai! Expected: ${item.destBin}`)
        return
      }
      break
  }

  // Check if all scans complete for this item
  if (item.boxScanned && item.sourceBinScanned && item.destBinScanned) {
    item.status = 'completed'
    if (!item.actualQty) {
      item.actualQty = item.qty
    }
  }

  closeQRModal()
}

const completeTO = () => {
  if (!selectedTO.value || !canCompleteTO.value) return

  // Update stock transactions (simulation)
  selectedTO.value.items.forEach(item => {
    console.log(`Stock Transaction:`)
    console.log(`- Reduce ${item.actualQty || item.qty} ${item.uom} from ${item.sourceBin}`)
    console.log(`- Add ${item.actualQty || item.qty} ${item.uom} to ${item.destBin}`)
  })

  // Update TO status
  selectedTO.value.status = 'Completed'
  selectedTO.value.items.forEach(item => {
    item.status = 'completed'
  })

  // Update original TO in array
  const originalIndex = transferOrders.value.findIndex(t => t.id === selectedTO.value!.id)
  if (originalIndex !== -1) {
    transferOrders.value[originalIndex] = { ...selectedTO.value }
  }

  alert('Transfer Order berhasil diselesaikan!')
  closeDetailModal()
}

const printTO = (to: TransferOrder) => {
  // Generate PDF content simulation
  const printContent = `
    TRANSFER ORDER
    
    TO Number: ${to.toNumber}
    Creation Date: ${formatDate(to.creationDate)}
    Warehouse: ${to.warehouse}
    Transaction Type: ${to.type}
    ${to.reservationNo ? `No Reservasi: ${to.reservationNo}` : ''}
    
    DAFTAR ITEM:
    ${to.items.map((item, index) => `
    ${index + 1}. ${item.itemCode} - ${item.materialName}
       Source Bin: ${item.sourceBin}
       Dest Bin: ${item.destBin}
       Qty: ${item.actualQty || item.qty} ${item.uom}
    `).join('')}
    
    TANDA TANGAN:
    Diserahkan: ________________  Tgl: _______
    Diterima:   ________________  Tgl: _______
    Mengetahui: ________________  Tgl: _______
  `

  // Create printable window
  const printWindow = window.open('', '_blank')
  if (printWindow) {
    printWindow.document.write(`
      <html>
        <head>
          <title>Transfer Order - ${to.toNumber}</title>
          <style>
            body { font-family: Arial, sans-serif; padding: 20px; }
            pre { white-space: pre-wrap; }
          </style>
        </head>
        <body>
          <pre>${printContent}</pre>
        </body>
      </html>
    `)
    printWindow.document.close()
    printWindow.print()
  }
}

// Utility functions
const formatDate = (date: Date) => {
  return new Intl.DateTimeFormat('id-ID', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit'
  }).format(date)
}

const getTypeClass = (type: string) => {
  const classes = {
    'Putaway': 'bg-blue-100 text-blue-800',
    'Transfer': 'bg-green-100 text-green-800',
    'Picking': 'bg-purple-100 text-purple-800'
  }
  return classes[type as keyof typeof classes] || 'bg-gray-100 text-gray-800'
}

const getStatusClass = (status: string) => {
  const classes = {
    'Pending': 'bg-yellow-100 text-yellow-800',
    'In Progress': 'bg-blue-100 text-blue-800',
    'Completed': 'bg-green-100 text-green-800'
  }
  return classes[status as keyof typeof classes] || 'bg-gray-100 text-gray-800'
}

const getItemStatusClass = (status: string) => {
  const classes = {
    'pending': 'bg-yellow-100 text-yellow-800',
    'in_progress': 'bg-blue-100 text-blue-800',
    'completed': 'bg-green-100 text-green-800'
  }
  return classes[status as keyof typeof classes] || 'bg-gray-100 text-gray-800'
}

// Initialize dummy data
onMounted(() => {
  const dummyData: TransferOrder[] = [
    {
      id: '1',
      toNumber: 'TO-2024-09-001',
      creationDate: new Date('2024-09-18T08:00:00'),
      warehouse: 'WH-001',
      type: 'Putaway',
      status: 'Pending',
      reservationNo: 'RSV-2024-045',
      items: [
        {
          itemCode: 'CHM-001',
          materialName: 'Sodium Chloride',
          sourceBin: 'QTN-A-01',
          destBin: 'STD-A-02-01',
          qty: 50,
          uom: 'KG',
          status: 'pending',
          boxScanned: false,
          sourceBinScanned: false,
          destBinScanned: false
        },
        {
          itemCode: 'CHM-002',
          materialName: 'Calcium Carbonate',
          sourceBin: 'QTN-A-02',
          destBin: 'STD-A-02-02',
          qty: 25,
          uom: 'KG',
          status: 'pending',
          boxScanned: false,
          sourceBinScanned: false,
          destBinScanned: false
        }
      ]
    },
    {
      id: '2',
      toNumber: 'TO-2024-09-002',
      creationDate: new Date('2024-09-18T09:15:00'),
      warehouse: 'WH-001',
      type: 'Transfer',
      status: 'In Progress',
      items: [
        {
          itemCode: 'CHM-003',
          materialName: 'Potassium Hydroxide',
          sourceBin: 'STD-A-01-05',
          destBin: 'STD-B-02-03',
          qty: 15,
          uom: 'L',
          status: 'in_progress',
          boxScanned: true,
          sourceBinScanned: false,
          destBinScanned: false
        }
      ]
    },
    {
      id: '3',
      toNumber: 'TO-2024-09-003',
      creationDate: new Date('2024-09-17T14:30:00'),
      warehouse: 'WH-001',
      type: 'Picking',
      status: 'Completed',
      items: [
        {
          itemCode: 'CHM-004',
          materialName: 'Magnesium Sulfate',
          sourceBin: 'STD-C-01-01',
          destBin: 'SHIP-OUT-01',
          qty: 30,
          actualQty: 30,
          uom: 'KG',
          status: 'completed',
          boxScanned: true,
          sourceBinScanned: true,
          destBinScanned: true
        }
      ]
    },
    {
      id: '4',
      toNumber: 'TO-2024-09-004',
      creationDate: new Date('2024-09-18T10:45:00'),
      warehouse: 'WH-002',
      type: 'Putaway',
      status: 'Pending',
      reservationNo: 'RSV-2024-046',
      items: [
        {
          itemCode: 'CHM-005',
          materialName: 'Hydrochloric Acid',
          sourceBin: 'QTN-B-01',
          destBin: 'HAZ-A-01-01',
          qty: 20,
          uom: 'L',
          status: 'pending',
          boxScanned: false,
          sourceBinScanned: false,
          destBinScanned: false
        }
      ]
    }
  ]

  transferOrders.value = dummyData
})
</script>