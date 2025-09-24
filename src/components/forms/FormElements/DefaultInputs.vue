<template>
  <div class="space-y-6">
    <!-- Header dengan tombol Buat Penerimaan -->
    <div class="flex justify-between items-center">
      <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Daftar Penerimaan</h2>
      <button
        @click="showModal = true"
        class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 transition-colors"
      >
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/>
        </svg>
        Buat Penerimaan
      </button>
    </div>

    <!-- Tabel Daftar Penerimaan -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow overflow-hidden">
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
          <thead class="bg-gray-50 dark:bg-gray-700">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No PO</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No Surat Jalan</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Supplier</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Tanggal Terima</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No Kendaraan</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Status</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Aksi</th>
            </tr>
          </thead>
          <tbody class="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
            <tr v-for="shipment in shipments" :key="shipment.id" class="hover:bg-gray-50 dark:hover:bg-gray-700">
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ shipment.noPo }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ shipment.noSuratJalan }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ shipment.supplier }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ formatDate(shipment.tanggalTerima) }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ shipment.noKendaraan }}</td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span :class="getStatusClass(shipment.status)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                  {{ shipment.status }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                <div class="flex space-x-2">
                  <button @click="viewDetail(shipment)" class="bg-indigo-100 dark:bg-indigo-900 text-indigo-700 dark:text-indigo-300 hover:bg-indigo-200 dark:hover:bg-indigo-800 px-2 py-1 rounded text-xs">Detail</button>
                  <button @click="printChecklist(shipment)" class="bg-green-100 dark:bg-green-900 text-green-700 dark:text-green-300 hover:bg-green-200 dark:hover:bg-green-800 px-2 py-1 rounded text-xs">Cetak Checklist</button>
                  <button @click="showQRModal(shipment)" class="bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300 hover:bg-blue-200 dark:hover:bg-blue-800 px-2 py-1 rounded text-xs">Cetak Label QR</button>
                  <button v-if="shipment.status === 'Arrived'" @click="lanjutQC(shipment)" class="bg-orange-100 dark:bg-orange-900 text-orange-700 dark:text-orange-300 hover:bg-orange-200 dark:hover:bg-orange-800 px-2 py-1 rounded text-xs">Lanjut QC</button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Modal Form Buat Penerimaan -->
    <div v-if="showModal" class="fixed inset-0 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: #2b333fab;">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-6xl w-full mx-4 max-h-[90vh] overflow-y-auto">
        <div class="p-6">
          <!-- Header Modal -->
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Buat Penerimaan Baru</h3>
            <button @click="closeModal" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>

          <!-- Form Header -->
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mb-6">
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">No PO *</label>
              <select v-model="newShipment.noPo" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                <option value="">Pilih No PO</option>
                <option v-for="po in dummyPOs" :key="po" :value="po">{{ po }}</option>
              </select>
            </div>
            
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">No Surat Jalan *</label>
              <input v-model="newShipment.noSuratJalan" type="text" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Supplier *</label>
              <select v-model="newShipment.supplier" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                <option value="">Pilih Supplier</option>
                <option v-for="supplier in dummySuppliers" :key="supplier" :value="supplier">{{ supplier }}</option>
              </select>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">No Kendaraan *</label>
              <input v-model="newShipment.noKendaraan" type="text" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Nama Driver *</label>
              <input v-model="newShipment.namaDriver" type="text" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Tanggal & Waktu Terima</label>
              <input v-model="newShipment.tanggalTerima" type="datetime-local" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Kategori</label>
              <select v-model="newShipment.kategori" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                <option value="">Pilih Kategori</option>
                <option value="Raw Material">Raw Material</option>
                <option value="Packaging Material">Packaging Material</option>
                <option value="Spare Part">Spare Part</option>
                <option value="Office Supply">Office Supply</option>
              </select>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Upload Dokumen</label>
              <input type="file" multiple accept="image/*,.pdf" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>
          </div>

          <!-- Detail Items -->
          <div class="border-t border-gray-200 dark:border-gray-600 pt-6">
            <div class="flex justify-between items-center mb-4">
              <h4 class="text-lg font-medium text-gray-900 dark:text-white">Detail Material</h4>
              <button @click="addNewItem" class="bg-green-600 hover:bg-green-700 text-white px-3 py-1 rounded text-sm">
                + Tambah Baris
              </button>
            </div>

            <div class="overflow-x-auto border border-gray-200 dark:border-gray-600 rounded-lg">
              <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-600" style="min-width: 1200px;">
                <thead class="bg-gray-50 dark:bg-gray-700">
                  <tr>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Kode Item</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Nama Material</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Batch/Mfg Lot</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Exp Date</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Qty Wadah</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Qty Unit</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Kondisi</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">CoA</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Label Mfg</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Status QC</th>
                    <th class="px-3 py-2 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase whitespace-nowrap">Aksi</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200 dark:divide-gray-600 bg-white dark:bg-gray-800">
                  <tr v-for="(item, index) in newShipment.items" :key="index">
                    <td class="px-3 py-2 whitespace-nowrap">
                      <select v-model="item.kodeItem" @change="updateNamaMaterial(index)" class="w-32 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                        <option value="">Pilih SKU</option>
                        <option v-for="sku in dummySKUs" :key="sku.code" :value="sku.code">{{ sku.code }}</option>
                      </select>
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <input v-model="item.namaMaterial" type="text" readonly class="w-40 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <input v-model="item.batchLot" type="text" class="w-28 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <input v-model="item.expDate" type="date" class="w-36 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <input v-model="item.qtyWadah" type="number" class="w-24 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <input v-model="item.qtyUnit" type="number" class="w-24 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <select v-model="item.kondisi" class="w-28 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                        <option value="">Pilih</option>
                        <option value="Baik">Baik</option>
                        <option value="Tidak Baik">Tidak Baik</option>
                      </select>
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <select v-model="item.coa" class="w-24 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                        <option value="">Pilih</option>
                        <option value="Ada">Ada</option>
                        <option value="Tidak Ada">Tidak Ada</option>
                      </select>
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <select v-model="item.labelMfg" class="w-24 text-sm border border-gray-300 dark:border-gray-600 rounded px-2 py-1 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                        <option value="">Pilih</option>
                        <option value="Ada">Ada</option>
                        <option value="Tidak Ada">Tidak Ada</option>
                      </select>
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <span class="text-sm text-gray-600 dark:text-gray-400">{{ item.statusQC }}</span>
                    </td>
                    <td class="px-3 py-2 whitespace-nowrap">
                      <button @click="removeItem(index)" class="bg-red-100 dark:bg-red-900 text-red-700 dark:text-red-300 hover:bg-red-200 dark:hover:bg-red-800 px-2 py-1 rounded text-xs">Hapus</button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <!-- Footer Modal -->
          <div class="flex justify-end space-x-3 mt-6 pt-6 border-t border-gray-200 dark:border-gray-600">
            <button @click="closeModal" class="px-4 py-2 text-gray-700 dark:text-gray-300 bg-gray-200 dark:bg-gray-600 rounded-md hover:bg-gray-300 dark:hover:bg-gray-500">
              Batal
            </button>
            <button @click="saveShipment" :disabled="!isFormValid" class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 disabled:bg-gray-400">
              Simpan
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal QR Code -->
    <div v-if="showQRCodeModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: #2b333fab;"0>
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-4xl w-full mx-4 max-h-[80vh] overflow-y-auto">
        <div class="p-6">
          <!-- Header Modal QR -->
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Label QR Code - {{ selectedShipment?.noSuratJalan }}</h3>
            <button @click="closeQRModal" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>

          <!-- Info Shipment -->
          <div class="bg-gray-50 dark:bg-gray-700 rounded-lg p-4 mb-6">
            <div class="grid grid-cols-2 gap-4 text-sm">
              <div>
                <span class="font-medium text-gray-700 dark:text-gray-300">No PO:</span>
                <span class="ml-2 text-gray-900 dark:text-white">{{ selectedShipment?.noPo }}</span>
              </div>
              <div>
                <span class="font-medium text-gray-700 dark:text-gray-300">Supplier:</span>
                <span class="ml-2 text-gray-900 dark:text-white">{{ selectedShipment?.supplier }}</span>
              </div>
              <div>
                <span class="font-medium text-gray-700 dark:text-gray-300">No Kendaraan:</span>
                <span class="ml-2 text-gray-900 dark:text-white">{{ selectedShipment?.noKendaraan }}</span>
              </div>
              <div>
                <span class="font-medium text-gray-700 dark:text-gray-300">Tanggal:</span>
                <span class="ml-2 text-gray-900 dark:text-white">{{ formatDate(selectedShipment?.tanggalTerima) }}</span>
              </div>
            </div>
          </div>

          <!-- Daftar QR Items -->
          <div class="space-y-4">
            <h4 class="text-md font-medium text-gray-900 dark:text-white">Daftar QR Code per Item:</h4>
            <div v-if="selectedShipment?.items && selectedShipment.items.length > 0" class="space-y-4">
              <div v-for="(item, index) in selectedShipment.items" :key="index" class="border border-gray-200 dark:border-gray-600 rounded-lg p-4">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                  <!-- Info Item -->
                  <div>
                    <div class="font-medium text-gray-900 dark:text-white mb-2">{{ item.kodeItem }} - {{ item.namaMaterial }}</div>
                    <div class="text-sm text-gray-600 dark:text-gray-400 space-y-1">
                      <div>Batch: {{ item.batchLot }}</div>
                      <div>Qty: {{ item.qtyUnit }}</div>
                      <div>Exp: {{ item.expDate }}</div>
                    </div>
                    <div class="bg-gray-100 dark:bg-gray-600 p-2 rounded font-mono text-xs text-gray-800 dark:text-gray-200 mt-3">
                      <strong>QR Content:</strong><br>{{ item.qrCode }}
                    </div>
                    <div class="flex space-x-2 mt-3">
                      <button @click="downloadQR(item)" class="bg-green-600 hover:bg-green-700 text-white px-3 py-1 rounded text-sm">
                        Download QR
                      </button>
                      <button @click="printSingleQR(item)" class="bg-blue-600 hover:bg-blue-700 text-white px-3 py-1 rounded text-sm">
                        Cetak QR
                      </button>
                    </div>
                  </div>
                  
                  <!-- Preview QR -->
                  <div class="flex flex-col items-center justify-center">
                    <div class="bg-white p-4 rounded-lg border-2 border-gray-300 mb-2">
                      <canvas :data-qr-canvas="index" class="block"></canvas>
                    </div>
                    <div class="text-xs text-gray-500 dark:text-gray-400 text-center">
                      Preview QR Code
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div v-else class="text-center py-8 text-gray-500 dark:text-gray-400">
              Belum ada item untuk shipment ini
            </div>
          </div>

          <!-- Footer Modal QR -->
          <div class="flex justify-end space-x-3 mt-6 pt-6 border-t border-gray-200 dark:border-gray-600">
            <button @click="closeQRModal" class="px-4 py-2 text-gray-700 dark:text-gray-300 bg-gray-200 dark:bg-gray-600 rounded-md hover:bg-gray-300 dark:hover:bg-gray-500">
              Tutup
            </button>
            <button @click="printAllQR" class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">
              Cetak Semua QR
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

// Data reaktif
const showModal = ref(false)
const showQRCodeModal = ref(false)
const selectedShipment = ref(null)
const shipments = ref([])

// Data dummy untuk dropdown
const dummyPOs = ref(['PO-001/2025', 'PO-002/2025', 'PO-003/2025'])
const dummySuppliers = ref(['PT Supplier A', 'PT Supplier B', 'CV Supplier C'])
const dummySKUs = ref([
  { code: 'RM-001', name: 'Tepung Terigu' },
  { code: 'RM-002', name: 'Gula Pasir' },
  { code: 'PM-001', name: 'Kardus Box 20x30' },
  { code: 'PM-002', name: 'Plastik Wrap' }
])

// Form data
const newShipment = ref({
  noPo: '',
  noSuratJalan: '',
  supplier: '',
  noKendaraan: '',
  namaDriver: '',
  tanggalTerima: '',
  kategori: '',
  items: []
})

// Data dummy untuk tabel
const initDummyData = () => {
  shipments.value = [
    {
      id: 1,
      noPo: 'PO-001/2025',
      noSuratJalan: 'SJ-001/2025',
      supplier: 'PT Supplier A',
      tanggalTerima: '2025-09-18T10:30:00',
      noKendaraan: 'B 1234 CD',
      status: 'Arrived',
      items: [
        {
          kodeItem: 'RM-001',
          namaMaterial: 'Tepung Terigu',
          batchLot: 'BATCH001',
          expDate: '2025-12-31',
          qtyUnit: '50',
          qrCode: 'IN/20250918/0001|RM-001|BATCH001|50|2025-12-31'
        },
        {
          kodeItem: 'PM-001',
          namaMaterial: 'Kardus Box 20x30',
          batchLot: 'BATCH002',
          expDate: '2026-06-30',
          qtyUnit: '100',
          qrCode: 'IN/20250918/0001|PM-001|BATCH002|100|2026-06-30'
        }
      ]
    },
    {
      id: 2,
      noPo: 'PO-002/2025',
      noSuratJalan: 'SJ-002/2025',
      supplier: 'PT Supplier B',
      tanggalTerima: '2025-09-17T14:15:00',
      noKendaraan: 'B 5678 EF',
      status: 'QC',
      items: [
        {
          kodeItem: 'RM-002',
          namaMaterial: 'Gula Pasir',
          batchLot: 'BATCH003',
          expDate: '2025-11-15',
          qtyUnit: '25',
          qrCode: 'IN/20250917/0001|RM-002|BATCH003|25|2025-11-15'
        }
      ]
    },
    {
      id: 3,
      noPo: 'PO-003/2025',
      noSuratJalan: 'SJ-003/2025',
      supplier: 'CV Supplier C',
      tanggalTerima: '2025-09-16T09:45:00',
      noKendaraan: 'B 9012 GH',
      status: 'Completed',
      items: []
    }
  ]
}

// Computed
const isFormValid = computed(() => {
  return newShipment.value.noPo && 
         newShipment.value.noSuratJalan && 
         newShipment.value.supplier && 
         newShipment.value.noKendaraan && 
         newShipment.value.namaDriver &&
         newShipment.value.items.length > 0
})

// Methods
const closeModal = () => {
  showModal.value = false
  resetForm()
}

const closeQRModal = () => {
  showQRCodeModal.value = false
  selectedShipment.value = null
}

const resetForm = () => {
  newShipment.value = {
    noPo: '',
    noSuratJalan: '',
    supplier: '',
    noKendaraan: '',
    namaDriver: '',
    tanggalTerima: new Date().toISOString().slice(0, 16),
    kategori: '',
    items: []
  }
}

const addNewItem = () => {
  newShipment.value.items.push({
    kodeItem: '',
    namaMaterial: '',
    batchLot: '',
    expDate: '',
    qtyWadah: '',
    qtyUnit: '',
    kondisi: '',
    coa: '',
    labelMfg: '',
    statusQC: 'To QC'
  })
}

const removeItem = (index) => {
  newShipment.value.items.splice(index, 1)
}

const updateNamaMaterial = (index) => {
  const selectedSKU = dummySKUs.value.find(sku => sku.code === newShipment.value.items[index].kodeItem)
  if (selectedSKU) {
    newShipment.value.items[index].namaMaterial = selectedSKU.name
    // Auto set status QC based on SKU type
    if (selectedSKU.code.startsWith('RM-')) {
      newShipment.value.items[index].statusQC = 'To QC'
    } else {
      newShipment.value.items[index].statusQC = 'Direct Putaway'
    }
  }
}

const saveShipment = () => {
  // Generate ID dan incoming number
  const newId = shipments.value.length + 1
  const today = new Date().toISOString().slice(0, 10).replace(/-/g, '')
  const incomingNumber = `IN/${today}/${String(newId).padStart(4, '0')}`
  
  // Buat record shipment baru
  const shipment = {
    id: newId,
    incomingNumber,
    noPo: newShipment.value.noPo,
    noSuratJalan: newShipment.value.noSuratJalan,
    supplier: newShipment.value.supplier,
    tanggalTerima: newShipment.value.tanggalTerima,
    noKendaraan: newShipment.value.noKendaraan,
    namaDriver: newShipment.value.namaDriver,
    kategori: newShipment.value.kategori,
    status: 'Arrived',
    items: newShipment.value.items.map(item => ({
      ...item,
      qrCode: generateQR(incomingNumber, item.kodeItem, item.batchLot, item.qtyUnit, item.expDate)
    }))
  }
  
  shipments.value.unshift(shipment)
  
  alert(`Penerimaan berhasil disimpan dengan nomor: ${incomingNumber}`)
  closeModal()
}

const generateQR = (shipmentId, itemId, lot, qty, expDate) => {
  return `${shipmentId}|${itemId}|${lot}|${qty}|${expDate}`
}

const formatDate = (dateString) => {
  const date = new Date(dateString)
  return date.toLocaleString('id-ID', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit'
  })
}

const getStatusClass = (status) => {
  const classes = {
    'Draft': 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300',
    'Arrived': 'bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-300',
    'QC': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
    'Completed': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300'
  }
  return classes[status] || 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300'
}

// Action handlers
const viewDetail = (shipment) => {
  alert(`Melihat detail shipment: ${shipment.incomingNumber || shipment.noSuratJalan}`)
}

const printChecklist = (shipment) => {
  alert(`Mencetak checklist untuk: ${shipment.incomingNumber || shipment.noSuratJalan}`)
}

const showQRModal = (shipment) => {
  selectedShipment.value = shipment
  showQRCodeModal.value = true
  // Generate QR codes after modal is shown
  setTimeout(() => {
    generateQRCodes()
  }, 100)
}

const generateQRCodes = () => {
  if (!selectedShipment.value?.items) return
  
  selectedShipment.value.items.forEach((item, index) => {
    const canvas = document.querySelector(`canvas[data-qr-canvas="${index}"]`)
    if (canvas) {
      const ctx = canvas.getContext('2d')
      canvas.width = 150
      canvas.height = 150
      
      // Simple QR code placeholder (you can integrate real QR library here)
      const size = 150
      const moduleSize = size / 21 // 21x21 modules for simple QR
      
      // Create simple pattern based on QR content
      const content = item.qrCode
      let hash = 0
      for (let i = 0; i < content.length; i++) {
        hash = ((hash << 5) - hash + content.charCodeAt(i)) & 0xffffffff
      }
      
      // Clear canvas with white background
      ctx.fillStyle = '#ffffff'
      ctx.fillRect(0, 0, size, size)
      ctx.fillStyle = '#000000'
      
      // Draw simple QR pattern
      for (let row = 0; row < 21; row++) {
        for (let col = 0; col < 21; col++) {
          // Create pattern based on hash and position
          const shouldFill = ((hash >> ((row + col) % 32)) & 1) === 1 || 
                           (row < 7 && col < 7 && (row === 0 || row === 6 || col === 0 || col === 6)) || // Top-left finder
                           (row < 7 && col >= 14 && (row === 0 || row === 6 || col === 14 || col === 20)) || // Top-right finder  
                           (row >= 14 && col < 7 && (row === 14 || row === 20 || col === 0 || col === 6)) // Bottom-left finder
          
          if (shouldFill) {
            ctx.fillRect(col * moduleSize, row * moduleSize, moduleSize - 0.5, moduleSize - 0.5)
          }
        }
      }
      
      // Add center squares for finder patterns
      ctx.fillRect(2 * moduleSize, 2 * moduleSize, 3 * moduleSize, 3 * moduleSize) // Top-left
      ctx.fillRect(16 * moduleSize, 2 * moduleSize, 3 * moduleSize, 3 * moduleSize) // Top-right  
      ctx.fillRect(2 * moduleSize, 16 * moduleSize, 3 * moduleSize, 3 * moduleSize) // Bottom-left
    }
  })
}

const downloadQR = (item) => {
  const index = selectedShipment.value.items.findIndex(i => i === item)
  const canvas = document.querySelector(`canvas[data-qr-canvas="${index}"]`)
  
  if (canvas) {
    // Create download link
    const link = document.createElement('a')
    link.download = `QR_${item.kodeItem}_${item.batchLot}.png`
    link.href = canvas.toDataURL('image/png')
    link.click()
  }
}

const printSingleQR = (item) => {
  const index = selectedShipment.value.items.findIndex(i => i === item)
  const canvas = document.querySelector(`canvas[data-qr-canvas="${index}"]`)
  
  if (canvas) {
    // Create print window with formatted label
    const printWindow = window.open('', '_blank')
    const qrDataURL = canvas.toDataURL('image/png')
    
    printWindow.document.write(`
      <html>
        <head>
          <title>QR Label - ${item.kodeItem}</title>
          <style>
            body { font-family: Arial, sans-serif; margin: 20px; }
            .label { border: 2px solid #000; padding: 15px; width: 300px; text-align: center; }
            .qr-code { margin: 10px 0; }
            .item-info { font-size: 12px; margin: 5px 0; }
            .item-code { font-weight: bold; font-size: 14px; }
            @media print {
              body { margin: 0; }
              .label { border: 2px solid #000; page-break-after: always; }
            }
          </style>
        </head>
        <body>
          <div class="label">
            <div class="item-code">${item.kodeItem}</div>
            <div class="item-info">${item.namaMaterial}</div>
            <div class="qr-code">
              <img src="${qrDataURL}" width="120" height="120">
            </div>
            <div class="item-info">Batch: ${item.batchLot}</div>
            <div class="item-info">Qty: ${item.qtyUnit}</div>
            <div class="item-info">Exp: ${item.expDate}</div>
            <div class="item-info">PO: ${selectedShipment.value.noPo}</div>
          </div>
        </body>
      </html>
    `)
    
    printWindow.document.close()
    printWindow.focus()
    
    // Auto print after loading
    setTimeout(() => {
      printWindow.print()
      printWindow.close()
    }, 500)
  }
}

const printAllQR = () => {
  if (selectedShipment.value?.items?.length > 0) {
    // Create print window with all labels
    const printWindow = window.open('', '_blank')
    let labelsHTML = ''
    
    selectedShipment.value.items.forEach((item, index) => {
      const canvas = document.querySelector(`canvas[data-qr-canvas="${index}"]`)
      if (canvas) {
        const qrDataURL = canvas.toDataURL('image/png')
        labelsHTML += `
          <div class="label">
            <div class="item-code">${item.kodeItem}</div>
            <div class="item-info">${item.namaMaterial}</div>
            <div class="qr-code">
              <img src="${qrDataURL}" width="120" height="120">
            </div>
            <div class="item-info">Batch: ${item.batchLot}</div>
            <div class="item-info">Qty: ${item.qtyUnit}</div>
            <div class="item-info">Exp: ${item.expDate}</div>
            <div class="item-info">PO: ${selectedShipment.value.noPo}</div>
          </div>
        `
      }
    })
    
    printWindow.document.write(`
      <html>
        <head>
          <title>All QR Labels - ${selectedShipment.value.noSuratJalan}</title>
          <style>
            body { font-family: Arial, sans-serif; margin: 20px; }
            .label { 
              border: 2px solid #000; 
              padding: 15px; 
              width: 300px; 
              text-align: center; 
              margin-bottom: 20px;
              display: inline-block;
              vertical-align: top;
            }
            .qr-code { margin: 10px 0; }
            .item-info { font-size: 12px; margin: 5px 0; }
            .item-code { font-weight: bold; font-size: 14px; }
            @media print {
              body { margin: 0; }
              .label { 
                border: 2px solid #000; 
                page-break-inside: avoid;
                margin: 10px;
              }
            }
          </style>
        </head>
        <body>
          ${labelsHTML}
        </body>
      </html>
    `)
    
    printWindow.document.close()
    printWindow.focus()
    
    setTimeout(() => {
      printWindow.print()
      printWindow.close()
    }, 500)
  } else {
    alert('Tidak ada item untuk dicetak')
  }
}

const lanjutQC = (shipment) => {
  alert(`Melanjutkan ke QC untuk: ${shipment.incomingNumber || shipment.noSuratJalan}`)
}

// Lifecycle
onMounted(() => {
  initDummyData()
  resetForm()
})
</script>
