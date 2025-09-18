<template>
  <div class="space-y-6">
    <!-- Header -->
    <div class="flex justify-between items-center">
      <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Label Karantina</h2>
      <div class="flex items-center space-x-4">
        <div class="text-sm text-gray-600 dark:text-gray-400">
          Total barang karantina: {{ karantinaItems.length }}
        </div>
        <!-- QR Scanner Button -->
        <button @click="openQRScanner" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 transition-colors">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 20h4M4 12h4m12 0h2M4 4h16a2 2 0 012 2v12a2 2 0 01-2 2H4a2 2 0 01-2-2V6a2 2 0 012-2z"/>
          </svg>
          Scan QR Code
        </button>
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
          <button @click="filterStatus = 'Karantina'" :class="filterStatus === 'Karantina' ? 'bg-yellow-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-yellow-500 hover:text-white">
            Karantina
          </button>
          <button @click="filterStatus = 'Release'" :class="filterStatus === 'Release' ? 'bg-green-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-green-500 hover:text-white">
            Release
          </button>
          <button @click="filterStatus = 'Reject'" :class="filterStatus === 'Reject' ? 'bg-red-600 text-white' : 'bg-gray-200 dark:bg-gray-600 text-gray-700 dark:text-gray-300'" class="px-3 py-1 rounded text-sm hover:bg-red-500 hover:text-white">
            Reject
          </button>
        </div>
      </div>
    </div>

    <!-- Tabel Label Karantina -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow overflow-hidden">
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
          <thead class="bg-gray-50 dark:bg-gray-700">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No Shipment</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Kode Item</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Nama Material</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No Lot</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Supplier</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Qty</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Exp Date</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Status</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Aksi</th>
            </tr>
          </thead>
          <tbody class="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
            <tr v-for="item in filteredItems" :key="item.id" class="hover:bg-gray-50 dark:hover:bg-gray-700">
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.noShipment }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 dark:text-white">{{ item.kodeItem }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.namaMaterial }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.noLot }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.supplier }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.qty }} {{ item.uom }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ formatDate(item.expDate) }}</td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span :class="getStatusClass(item.status)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                  {{ item.status }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                <div class="flex space-x-2">
                  <button @click="printQRLabel(item)" class="bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300 hover:bg-blue-200 dark:hover:bg-blue-800 px-2 py-1 rounded text-xs">
                    Cetak Label QR
                  </button>
                  <button v-if="item.status === 'Karantina'" @click="releaseItem(item)" class="bg-green-100 dark:bg-green-900 text-green-700 dark:text-green-300 hover:bg-green-200 dark:hover:bg-green-800 px-2 py-1 rounded text-xs">
                    Release
                  </button>
                  <button v-if="item.status === 'Karantina'" @click="rejectItem(item)" class="bg-red-100 dark:bg-red-900 text-red-700 dark:text-red-300 hover:bg-red-200 dark:hover:bg-red-800 px-2 py-1 rounded text-xs">
                    Reject
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Modal QR Scanner -->
    <div v-if="showQRScanner" class="fixed inset-0 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: rgba(43, 51, 63, 0.67);">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-md w-full mx-4">
        <div class="p-6">
          <div class="flex justify-between items-center mb-4">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Scan QR Code</h3>
            <button @click="closeQRScanner" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>
          
          <div class="space-y-4">
            <div class="bg-gray-100 dark:bg-gray-700 p-4 rounded-lg text-center">
              <svg class="w-16 h-16 mx-auto text-gray-400 mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 20h4M4 12h4m12 0h2M4 4h16a2 2 0 012 2v12a2 2 0 01-2 2H4a2 2 0 01-2-2V6a2 2 0 012-2z"/>
              </svg>
              <p class="text-sm text-gray-600 dark:text-gray-400">Arahkan kamera ke QR Code</p>
            </div>
            
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">Atau input manual QR Code:</label>
              <input v-model="manualQRInput" @keyup.enter="processQRCode(manualQRInput)" type="text" placeholder="Contoh: IN/20250918/0001|RM-001|BATCH001|50|2025-12-31|Karantina" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>
            
            <button @click="processQRCode(manualQRInput)" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-2 px-4 rounded-md">
              Process QR Code
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal Detail Item (dari QR Scan) -->
    <div v-if="showDetailModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-2xl w-full mx-4 max-h-[80vh] overflow-y-auto">
        <div class="p-6">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Detail Barang - {{ scannedItem?.kodeItem }}</h3>
            <button @click="closeDetailModal" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>

          <!-- Item Details -->
          <div v-if="scannedItem" class="space-y-4">
            <div class="bg-gray-50 dark:bg-gray-700 rounded-lg p-4">
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">No Shipment:</span>
                  <div class="text-gray-900 dark:text-white">{{ scannedItem.noShipment }}</div>
                </div>
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Kode Item:</span>
                  <div class="text-gray-900 dark:text-white font-medium">{{ scannedItem.kodeItem }}</div>
                </div>
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Nama Material:</span>
                  <div class="text-gray-900 dark:text-white">{{ scannedItem.namaMaterial }}</div>
                </div>
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">No Lot:</span>
                  <div class="text-gray-900 dark:text-white">{{ scannedItem.noLot }}</div>
                </div>
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Supplier:</span>
                  <div class="text-gray-900 dark:text-white">{{ scannedItem.supplier }}</div>
                </div>
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Quantity:</span>
                  <div class="text-gray-900 dark:text-white">{{ scannedItem.qty }} {{ scannedItem.uom }}</div>
                </div>
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Tanggal Datang:</span>
                  <div class="text-gray-900 dark:text-white">{{ formatDate(scannedItem.tglDatang) }}</div>
                </div>
                <div>
                  <span class="text-sm font-medium text-gray-700 dark:text-gray-300">Exp Date:</span>
                  <div class="text-gray-900 dark:text-white">{{ formatDate(scannedItem.expDate) }}</div>
                </div>
              </div>
              
              <!-- Status Badge -->
              <div class="mt-4 flex items-center justify-center">
                <span :class="getStatusClass(scannedItem.status)" class="px-6 py-2 text-lg font-bold rounded-lg">
                  {{ scannedItem.status.toUpperCase() }}
                </span>
              </div>
            </div>

            <!-- Action Buttons -->
            <div class="flex justify-center space-x-4 pt-4 border-t border-gray-200 dark:border-gray-600">
              <button v-if="scannedItem.status === 'Karantina'" @click="releaseScannedItem" class="bg-green-600 hover:bg-green-700 text-white px-6 py-2 rounded-md font-medium">
                Release Barang
              </button>
              <button v-if="scannedItem.status === 'Karantina'" @click="rejectScannedItem" class="bg-red-600 hover:bg-red-700 text-white px-6 py-2 rounded-md font-medium">
                Reject Barang
              </button>
              <button @click="printQRLabel(scannedItem)" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-2 rounded-md font-medium">
                Cetak Ulang Label
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
const karantinaItems = ref([])
const filterStatus = ref('ALL')
const showQRScanner = ref(false)
const showDetailModal = ref(false)
const scannedItem = ref(null)
const manualQRInput = ref('')

// Data dummy untuk testing
const initDummyData = () => {
  karantinaItems.value = [
    {
      id: 1,
      noShipment: 'IN/20250918/0001',
      kodeItem: 'RM-001',
      namaMaterial: 'Tepung Terigu',
      noLot: 'BATCH001',
      supplier: 'PT Supplier A',
      qty: 50,
      uom: 'KG',
      expDate: '2025-12-31',
      tglDatang: '2025-09-18',
      status: 'Karantina',
      qrCode: 'IN/20250918/0001|RM-001|BATCH001|50|2025-12-31|Karantina',
      lastUpdated: '2025-09-18T10:30:00',
      updatedBy: 'QC001'
    },
    {
      id: 2,
      noShipment: 'IN/20250918/0001',
      kodeItem: 'PM-001',
      namaMaterial: 'Kardus Box 20x30',
      noLot: 'BATCH002',
      supplier: 'PT Supplier A',
      qty: 100,
      uom: 'PCS',
      expDate: '2026-06-30',
      tglDatang: '2025-09-18',
      status: 'Release',
      qrCode: 'IN/20250918/0001|PM-001|BATCH002|100|2026-06-30|Release',
      lastUpdated: '2025-09-18T14:15:00',
      updatedBy: 'WH001'
    },
    {
      id: 3,
      noShipment: 'IN/20250917/0001',
      kodeItem: 'RM-002',
      namaMaterial: 'Gula Pasir',
      noLot: 'BATCH003',
      supplier: 'PT Supplier B',
      qty: 25,
      uom: 'KG',
      expDate: '2025-11-15',
      tglDatang: '2025-09-17',
      status: 'Karantina',
      qrCode: 'IN/20250917/0001|RM-002|BATCH003|25|2025-11-15|Karantina',
      lastUpdated: '2025-09-17T11:00:00',
      updatedBy: 'QC001'
    },
    {
      id: 4,
      noShipment: 'IN/20250916/0001',
      kodeItem: 'RM-003',
      namaMaterial: 'Garam Dapur',
      noLot: 'BATCH004',
      supplier: 'CV Supplier C',
      qty: 10,
      uom: 'KG',
      expDate: '2025-10-20',
      tglDatang: '2025-09-16',
      status: 'Reject',
      qrCode: 'IN/20250916/0001|RM-003|BATCH004|10|2025-10-20|Reject',
      lastUpdated: '2025-09-16T16:45:00',
      updatedBy: 'QC002'
    }
  ]
}

// Computed
const filteredItems = computed(() => {
  if (filterStatus.value === 'ALL') {
    return karantinaItems.value
  }
  return karantinaItems.value.filter(item => item.status === filterStatus.value)
})

// Methods
const getStatusClass = (status) => {
  const classes = {
    'Karantina': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
    'Release': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300',
    'Reject': 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300'
  }
  return classes[status] || 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300'
}

const formatDate = (dateString) => {
  return new Date(dateString).toLocaleDateString('id-ID')
}

const printQRLabel = (item) => {
  // Create Label Karantina
  const printWindow = window.open('', '_blank')
  
  printWindow.document.write(`
    <html>
      <head>
        <title>Label Karantina - ${item.kodeItem}</title>
        <style>
          body { font-family: Arial, sans-serif; margin: 20px; }
          .label { 
            border: 3px solid #000; 
            padding: 20px; 
            width: 350px; 
            text-align: center;
            ${item.status === 'Karantina' ? 'background-color: #fffbf0; border-color: #fbbf24;' : 
              item.status === 'Release' ? 'background-color: #f0fdf4; border-color: #22c55e;' : 
              'background-color: #fef2f2; border-color: #ef4444;'}
          }
          .status-banner { 
            font-weight: bold; 
            font-size: 24px; 
            padding: 10px; 
            margin-bottom: 15px;
            border-radius: 8px;
            ${item.status === 'Karantina' ? 'background-color: #fbbf24; color: #000;' : 
              item.status === 'Release' ? 'background-color: #22c55e; color: #fff;' : 
              'background-color: #ef4444; color: #fff;'}
          }
          .item-info { font-size: 14px; margin: 8px 0; text-align: left; }
          .item-name { font-weight: bold; font-size: 16px; margin-bottom: 10px; }
          .qr-placeholder { 
            width: 120px; 
            height: 120px; 
            border: 2px solid #000; 
            margin: 15px auto; 
            display: flex; 
            align-items: center; 
            justify-content: center; 
            background-color: white;
            font-size: 10px;
            text-align: center;
            word-break: break-all;
            padding: 5px;
          }
          @media print {
            body { margin: 0; }
            .label { page-break-after: always; }
          }
        </style>
      </head>
      <body>
        <div class="label">
          <div class="status-banner">${item.status.toUpperCase()}</div>
          <div class="item-name">${item.namaMaterial}</div>
          <div class="item-info"><strong>Kode:</strong> ${item.kodeItem}</div>
          <div class="item-info"><strong>No Lot:</strong> ${item.noLot}</div>
          <div class="item-info"><strong>Supplier:</strong> ${item.supplier}</div>
          <div class="item-info"><strong>Jumlah:</strong> ${item.qty} ${item.uom}</div>
          <div class="item-info"><strong>Tgl Datang:</strong> ${formatDate(item.tglDatang)}</div>
          <div class="item-info"><strong>Exp Date:</strong> ${formatDate(item.expDate)}</div>
          <div class="qr-placeholder">${item.qrCode}</div>
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

const releaseItem = (item) => {
  if (confirm(`Apakah Anda yakin ingin RELEASE barang ${item.kodeItem} - ${item.namaMaterial}?`)) {
    updateItemStatus(item.id, 'Release', 'WH001')
    alert(`Barang ${item.kodeItem} berhasil di-RELEASE!\nBarang akan masuk ke proses Putaway.`)
  }
}

const rejectItem = (item) => {
  if (confirm(`Apakah Anda yakin ingin REJECT barang ${item.kodeItem} - ${item.namaMaterial}?`)) {
    updateItemStatus(item.id, 'Reject', 'QC001')
    alert(`Barang ${item.kodeItem} berhasil di-REJECT!\nBarang akan masuk ke Return Supplier flow.`)
  }
}

const updateItemStatus = (itemId, newStatus, userId) => {
  const itemIndex = karantinaItems.value.findIndex(item => item.id === itemId)
  if (itemIndex !== -1) {
    karantinaItems.value[itemIndex].status = newStatus
    karantinaItems.value[itemIndex].lastUpdated = new Date().toISOString()
    karantinaItems.value[itemIndex].updatedBy = userId
    
    // Update QR Code with new status
    const qrParts = karantinaItems.value[itemIndex].qrCode.split('|')
    qrParts[5] = newStatus
    karantinaItems.value[itemIndex].qrCode = qrParts.join('|')
  }
}

// QR Scanner Methods
const openQRScanner = () => {
  showQRScanner.value = true
  manualQRInput.value = ''
}

const closeQRScanner = () => {
  showQRScanner.value = false
  manualQRInput.value = ''
}

const processQRCode = (qrContent) => {
  if (!qrContent) {
    alert('Silakan input QR Code terlebih dahulu!')
    return
  }
  
  // Parse QR Code: shipment_id|item_id|lot_no|qty|exp_date|status
  const qrParts = qrContent.split('|')
  if (qrParts.length !== 6) {
    alert('Format QR Code tidak valid!\nFormat yang benar: shipment_id|item_id|lot_no|qty|exp_date|status')
    return
  }
  
  const [shipmentId, itemId, lotNo] = qrParts
  
  // Find item based on QR content
  const foundItem = karantinaItems.value.find(item => 
    item.noShipment === shipmentId && 
    item.kodeItem === itemId && 
    item.noLot === lotNo
  )
  
  if (!foundItem) {
    alert('Barang tidak ditemukan dalam sistem!')
    return
  }
  
  scannedItem.value = foundItem
  showQRScanner.value = false
  showDetailModal.value = true
  manualQRInput.value = ''
}

const closeDetailModal = () => {
  showDetailModal.value = false
  scannedItem.value = null
}

const releaseScannedItem = () => {
  if (scannedItem.value) {
    releaseItem(scannedItem.value)
    closeDetailModal()
  }
}

const rejectScannedItem = () => {
  if (scannedItem.value) {
    rejectItem(scannedItem.value)
    closeDetailModal()
  }
}

// Lifecycle
onMounted(() => {
  initDummyData()
})
</script>