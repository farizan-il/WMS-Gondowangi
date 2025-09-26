<template>
  <div class="space-y-6">
    <!-- Header -->
    <div class="flex justify-between items-center">
      <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Daftar QC (Quality Control)</h2>
      <div class="text-sm text-gray-600 dark:text-gray-400">
        Total item menunggu QC: {{ itemsToQC.length }}
      </div>
      <button @click="openQRScanner" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 transition-colors">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 20h4M4 12h4m12 0h2M4 4h16a2 2 0 012 2v12a2 2 0 01-2 2H4a2 2 0 01-2-2V6a2 2 0 012-2z"/>
          </svg>
          Scan QR Code
        </button>
    </div>

    <!-- Tabel Daftar QC -->
    <div class="bg-white dark:bg-gray-800 rounded-lg shadow overflow-hidden">
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200 dark:divide-gray-700">
          <thead class="bg-gray-50 dark:bg-gray-700">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No Shipment / No PO</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">No Surat Jalan</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Supplier</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Item (Kode & Nama)</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Qty Received</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Status QC</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 dark:text-gray-300 uppercase tracking-wider">Aksi</th>
            </tr>
          </thead>
          <tbody class="bg-white dark:bg-gray-800 divide-y divide-gray-200 dark:divide-gray-700">
            <tr v-for="item in itemsToQC" :key="item.id" class="hover:bg-gray-50 dark:hover:bg-gray-700">
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">
                <div>{{ item.shipmentNumber }}</div>
                <div class="text-xs text-gray-500">{{ item.noPo }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.noSuratJalan }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.supplier }}</td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">
                <div class="font-medium">{{ item.kodeItem }}</div>
                <div class="text-xs text-gray-500 dark:text-gray-400">{{ item.namaMaterial }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900 dark:text-white">{{ item.qtyReceived }} {{ item.uom }}</td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span :class="getQCStatusClass(item.statusQC)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                  {{ item.statusQC }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                <div class="flex space-x-2">
                  <button v-if="item.statusQC === 'To QC'" @click="showItemDetail(item)" class="bg-blue-100 dark:bg-blue-900 text-blue-700 dark:text-blue-300 hover:bg-blue-200 dark:hover:bg-blue-800 px-3 py-1 rounded text-xs">
                    Periksa QC
                  </button>
                  <button v-if="item.statusQC === 'PASS'" @click="printGRSlip(item)" class="bg-green-100 dark:bg-green-900 text-green-700 dark:text-green-300 hover:bg-green-200 dark:hover:bg-green-800 px-2 py-1 rounded text-xs">
                    Cetak GR Slip
                  </button>
                  <button v-if="item.statusQC === 'REJECT'" @click="printReturnSlip(item)" class="bg-red-100 dark:bg-red-900 text-red-700 dark:text-red-300 hover:bg-red-200 dark:hover:bg-red-800 px-2 py-1 rounded text-xs">
                    Cetak Return Slip
                  </button>
                  <button v-if="item.statusQC !== 'To QC'" @click="printQRLabel(item)" class="bg-purple-100 dark:bg-purple-900 text-purple-700 dark:text-purple-300 hover:bg-purple-200 dark:hover:bg-purple-800 px-2 py-1 rounded text-xs">
                    Cetak Label QR
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Modal Detail Item (Step 1) -->
    <div v-if="showDetailModal" class="fixed inset-0 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: rgba(43, 51, 63, 0.67);">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-4xl w-full mx-4 max-h-[90vh] overflow-y-auto">
        <div class="p-6">
          <!-- Header Modal -->
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Detail Item QC - {{ selectedItem?.kodeItem }}</h3>
            <button @click="closeDetailModal" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>

          <!-- Detail Informasi -->
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">No Shipment</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white font-medium">{{ selectedItem?.shipmentNumber }}</div>
              </div>
              
              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">No PO</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white">{{ selectedItem?.noPo }}</div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">No Surat Jalan</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white">{{ selectedItem?.noSuratJalan }}</div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">Kode Item</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white font-medium">{{ selectedItem?.kodeItem }}</div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">Nama Material</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white">{{ selectedItem?.namaMaterial }}</div>
              </div>
            </div>

            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">Supplier</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white">{{ selectedItem?.supplier }}</div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">Quantity Received</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white font-medium">{{ selectedItem?.qtyReceived }} {{ selectedItem?.uom }}</div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">No Kendaraan</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white">{{ selectedItem?.noKendaraan }}</div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">Nama Driver</label>
                <div class="mt-1 text-sm text-gray-900 dark:text-white">{{ selectedItem?.namaDriver }}</div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300">Status QC</label>
                <span :class="getQCStatusClass(selectedItem?.statusQC)" class="inline-flex px-2 py-1 text-xs font-semibold rounded-full">
                  {{ selectedItem?.statusQC }}
                </span>
              </div>
            </div>
          </div>

          <!-- Footer Modal -->
          <div class="flex justify-end space-x-3 mt-6 pt-6 border-t border-gray-200 dark:border-gray-600">
            <button @click="closeDetailModal" class="px-4 py-2 text-gray-700 dark:text-gray-300 bg-gray-200 dark:bg-gray-600 rounded-md hover:bg-gray-300 dark:hover:bg-gray-500">
              Tutup
            </button>
            <button @click="openQCModal" class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700">
              Lanjut QC
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal Form QC (Step 2) -->
    <div v-if="showQCModal" class="fixed inset-0 bg-opacity-50 dark:bg-gray-900 dark:bg-opacity-75 backdrop-blur-sm flex items-center justify-center z-[9999]" style="background-color: rgba(43, 51, 63, 0.67);">
      <div class="bg-white dark:bg-gray-800 rounded-lg max-w-4xl w-full mx-4 max-h-[90vh] overflow-y-auto">
        <div class="p-6">
          <!-- Header Modal -->
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-lg font-semibold text-gray-900 dark:text-white">Form QC - {{ selectedItem?.kodeItem }}</h3>
            <button @click="closeQCModal" class="text-gray-400 hover:text-gray-600 dark:text-gray-500 dark:hover:text-gray-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
              </svg>
            </button>
          </div>

          <!-- Form Header Info -->
          <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mb-6">
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">No Form Checklist</label>
              <input v-model="qcForm.noFormChecklist" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>
            
            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Date</label>
              <input v-model="qcForm.date" type="datetime-local" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">No PO</label>
              <input v-model="qcForm.noPo" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">No Surat Jalan</label>
              <input v-model="qcForm.noSuratJalan" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Kode Item</label>
              <input v-model="qcForm.kodeItem" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Nama Material</label>
              <input v-model="qcForm.namaMaterial" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Reference</label>
              <input v-model="qcForm.reference" type="text" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Nama Supplier</label>
              <input v-model="qcForm.supplier" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Kategori</label>
              <select v-model="qcForm.kategori" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                <option value="">Pilih Kategori</option>
                <option value="Raw Material">Raw Material</option>
                <option value="Packaging Material">Packaging Material</option>
              </select>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">No Kendaraan</label>
              <input v-model="qcForm.noKendaraan" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Nama Driver</label>
              <input v-model="qcForm.namaDriver" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
            </div>
          </div>

          <!-- Form Quantity Info -->
          <div class="border-t border-gray-200 dark:border-gray-600 pt-6 mb-6">
            <h4 class="text-md font-medium text-gray-900 dark:text-white mb-4">Informasi Quantity</h4>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Jumlah Box Utuh</label>
                <input v-model="qcForm.jumlahBoxUtuh" type="number" @input="calculateTotal" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Qty Box Utuh</label>
                <input v-model="qcForm.qtyBoxUtuh" type="number" @input="calculateTotal" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Jumlah Box Tidak Utuh</label>
                <input v-model="qcForm.jumlahBoxTidakUtuh" type="number" @input="calculateTotal" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Qty Box Tidak Utuh</label>
                <input v-model="qcForm.qtyBoxTidakUtuh" type="number" @input="calculateTotal" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Jumlah Total Incoming</label>
                <input v-model="qcForm.totalIncoming" type="number" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">UoM</label>
                <input v-model="qcForm.uom" type="text" readonly class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md bg-gray-50 dark:bg-gray-600 text-gray-900 dark:text-white">
              </div>
            </div>
          </div>

          <!-- Hasil QC -->
          <div class="border-t border-gray-200 dark:border-gray-600 pt-6">
            <h4 class="text-md font-medium text-gray-900 dark:text-white mb-4">Hasil QC</h4>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div class="space-y-4">
                <div>
                  <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Defect Count</label>
                  <input v-model="qcForm.defectCount" type="number" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                </div>

                <div>
                  <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Catatan QC</label>
                  <textarea v-model="qcForm.catatanQC" rows="4" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white"></textarea>
                </div>

                <div>
                  <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">Upload Foto Bukti</label>
                  <input type="file" multiple accept="image/*" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
                </div>
              </div>

              <div class="space-y-4">
                <div>
                  <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-3">Hasil QC *</label>
                  <div class="space-y-2">
                    <label class="flex items-center">
                      <input v-model="qcForm.hasilQC" type="radio" value="PASS" class="mr-2 text-green-600 focus:ring-green-500">
                      <span class="text-sm text-gray-900 dark:text-white">PASS</span>
                    </label>
                    <label class="flex items-center">
                      <input v-model="qcForm.hasilQC" type="radio" value="REJECT" class="mr-2 text-red-600 focus:ring-red-500">
                      <span class="text-sm text-gray-900 dark:text-white">REJECT</span>
                    </label>
                  </div>
                </div>

                <div v-if="qcForm.hasilQC" class="mt-4 p-4 rounded-lg" :class="qcForm.hasilQC === 'PASS' ? 'bg-green-50 dark:bg-green-900 border border-green-200 dark:border-green-700' : 'bg-red-50 dark:bg-red-900 border border-red-200 dark:border-red-700'">
                  <div class="text-sm" :class="qcForm.hasilQC === 'PASS' ? 'text-green-800 dark:text-green-200' : 'text-red-800 dark:text-red-200'">
                    <strong>{{ qcForm.hasilQC === 'PASS' ? 'Akan digenerate:' : 'Akan digenerate:' }}</strong>
                    <ul class="mt-2 list-disc list-inside">
                      <li v-if="qcForm.hasilQC === 'PASS'">Good Receipt Slip</li>
                      <li v-if="qcForm.hasilQC === 'PASS'">Label Karantina QR (Status: KARANTINA)</li>
                      <li v-if="qcForm.hasilQC === 'REJECT'">Return Slip</li>
                      <li v-if="qcForm.hasilQC === 'REJECT'">Label QR (Status: REJECT)</li>
                    </ul>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Footer Modal -->
          <div class="flex justify-end space-x-3 mt-6 pt-6 border-t border-gray-200 dark:border-gray-600">
            <button @click="backToDetail" class="px-4 py-2 text-gray-700 dark:text-gray-300 bg-gray-200 dark:bg-gray-600 rounded-md hover:bg-gray-300 dark:hover:bg-gray-500">
              Kembali ke Detail
            </button>
            <button @click="submitQC" :disabled="!isQCFormValid" class="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 disabled:bg-gray-400">
              Simpan Hasil QC
            </button>
          </div>
        </div>
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
              <input v-model="manualQRInput" @keyup.enter="processQRCode(manualQRInput)" type="text" placeholder="Contoh: IN/20250918/0001|RM-001 atau IN/20250918/0002|PM-001" class="w-full px-3 py-2 border border-gray-300 dark:border-gray-600 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 bg-white dark:bg-gray-700 text-gray-900 dark:text-white">
              
              <!-- Contoh QR Code untuk testing -->
              <div class="mt-2 text-xs text-gray-500 dark:text-gray-400">
                <p>Contoh QR Code yang tersedia untuk testing:</p>
                <div class="mt-1 space-y-1">
                  <div class="cursor-pointer hover:text-blue-500" @click="manualQRInput = 'IN/20250918/0001|RM-001'">• IN/20250918/0001|RM-001 (Tepung Terigu)</div>
                  <div class="cursor-pointer hover:text-blue-500" @click="manualQRInput = 'IN/20250918/0002|PM-001'">• IN/20250918/0002|PM-001 (Plastik Kemasan)</div>
                  <div class="cursor-pointer hover:text-blue-500" @click="manualQRInput = 'IN/20250918/0003|RM-002'">• IN/20250918/0003|RM-002 (Gula Pasir)</div>
                </div>
              </div>
            </div>
            
            <button @click="processQRCode(manualQRInput)" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-2 px-4 rounded-md">
              Process QR Code
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
const showDetailModal = ref(false)
const showQCModal = ref(false)
const showQRScanner = ref(false)
const selectedItem = ref(null)
const itemsToQC = ref([])
const manualQRInput = ref('')

// Form QC data
const qcForm = ref({
  noFormChecklist: '',
  date: '',
  noPo: '',
  noSuratJalan: '',
  kodeItem: '',
  namaMaterial: '',
  serialNumber: '',
  reference: '',
  supplier: '',
  kategori: '',
  jumlahBoxUtuh: '',
  qtyBoxUtuh: '',
  jumlahBoxTidakUtuh: '',
  qtyBoxTidakUtuh: '',
  totalIncoming: '',
  uom: '',
  noKendaraan: '',
  namaDriver: '',
  defectCount: 0,
  catatanQC: '',
  hasilQC: ''
})

// Data dummy untuk testing
const initDummyData = () => {
  itemsToQC.value = [
    {
      id: 2,
      shipmentNumber: 'IN/20250918/0002',
      noPo: 'PO-002/2025',
      noSuratJalan: 'SJ-002/2025',
      supplier: 'PT Supplier B',
      kodeItem: 'PM-001',
      namaMaterial: 'Plastik Kemasan',
      qtyReceived: 100,
      uom: 'PCS',
      statusQC: 'PASS',
      noKendaraan: 'B 5678 EF',
      namaDriver: 'Ahmad Suryadi',
      kategori: 'Packaging Material'
    },
    {
      id: 3,
      shipmentNumber: 'IN/20250918/0003',
      noPo: 'PO-003/2025',
      noSuratJalan: 'SJ-003/2025',
      supplier: 'PT Supplier C',
      kodeItem: 'RM-002',
      namaMaterial: 'Gula Pasir',
      qtyReceived: 25,
      uom: 'KG',
      statusQC: 'REJECT',
      noKendaraan: 'B 9012 GH',
      namaDriver: 'Sari Wulandari',
      kategori: 'Raw Material'
    }
  ]
}

// Computed
const isQCFormValid = computed(() => {
  return qcForm.value.hasilQC && 
    qcForm.value.kategori &&
    qcForm.value.jumlahBoxUtuh !== '' &&
    qcForm.value.qtyBoxUtuh !== ''
})

// Methods
const getQCStatusClass = (status) => {
  const classes = {
    'To QC': 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900 dark:text-yellow-300',
    'PASS': 'bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-300',
    'REJECT': 'bg-red-100 text-red-800 dark:bg-red-900 dark:text-red-300'
  }
  return classes[status] || 'bg-gray-100 text-gray-800 dark:bg-gray-700 dark:text-gray-300'
}

// Step 1: Show item detail
const showItemDetail = (item) => {
  selectedItem.value = item
  showDetailModal.value = true
}

const closeDetailModal = () => {
  showDetailModal.value = false
  selectedItem.value = null
}

// Step 2: Open QC Form from detail
const openQCModal = () => {
  if (!selectedItem.value) return
  
  // Generate form checklist number
  const today = new Date().toISOString().slice(0, 10).replace(/-/g, '')
  const checklistNumber = `QC/${today}/${String(selectedItem.value.id).padStart(4, '0')}`
  
  // Fill form with item data
  qcForm.value = {
    noFormChecklist: checklistNumber,
    date: new Date().toISOString().slice(0, 16),
    noPo: selectedItem.value.noPo,
    noSuratJalan: selectedItem.value.noSuratJalan,
    kodeItem: selectedItem.value.kodeItem,
    namaMaterial: selectedItem.value.namaMaterial,
    serialNumber: '',
    reference: '',
    supplier: selectedItem.value.supplier,
    kategori: selectedItem.value.kategori,
    jumlahBoxUtuh: '',
    qtyBoxUtuh: '',
    jumlahBoxTidakUtuh: '',
    qtyBoxTidakUtuh: '',
    totalIncoming: selectedItem.value.qtyReceived,
    uom: selectedItem.value.uom,
    noKendaraan: selectedItem.value.noKendaraan,
    namaDriver: selectedItem.value.namaDriver,
    defectCount: 0,
    catatanQC: '',
    hasilQC: ''
  }
  
  showDetailModal.value = false
  showQCModal.value = true
}

const closeQCModal = () => {
  showQCModal.value = false
  selectedItem.value = null
  resetQCForm()
}

const backToDetail = () => {
  showQCModal.value = false
  showDetailModal.value = true
}

const resetQCForm = () => {
  qcForm.value = {
    noFormChecklist: '',
    date: '',
    noPo: '',
    noSuratJalan: '',
    kodeItem: '',
    namaMaterial: '',
    serialNumber: '',
    reference: '',
    supplier: '',
    kategori: '',
    jumlahBoxUtuh: '',
    qtyBoxUtuh: '',
    jumlahBoxTidakUtuh: '',
    qtyBoxTidakUtuh: '',
    totalIncoming: '',
    uom: '',
    noKendaraan: '',
    namaDriver: '',
    defectCount: 0,
    catatanQC: '',
    hasilQC: ''
  }
}

const calculateTotal = () => {
  const boxUtuh = parseInt(qcForm.value.qtyBoxUtuh) || 0
  const boxTidakUtuh = parseInt(qcForm.value.qtyBoxTidakUtuh) || 0
  qcForm.value.totalIncoming = boxUtuh + boxTidakUtuh
}

const submitQC = () => {
  if (!selectedItem.value) return
  
  // Update status item
  const itemIndex = itemsToQC.value.findIndex(item => item.id === selectedItem.value.id)
  if (itemIndex !== -1) {
    itemsToQC.value[itemIndex].statusQC = qcForm.value.hasilQC
  }
  
  // Generate documents based on result
  if (qcForm.value.hasilQC === 'PASS') {
    alert(`QC PASS berhasil disimpan!\nDigenerate: Good Receipt Slip & Label Karantina QR\nNo Form: ${qcForm.value.noFormChecklist}`)
  } else if (qcForm.value.hasilQC === 'REJECT') {
    alert(`QC REJECT berhasil disimpan!\nDigenerate: Return Slip & Label QR (Status: REJECT)\nNo Form: ${qcForm.value.noFormChecklist}`)
  }
  
  closeQCModal()
}

// QR Scanner functions
const openQRScanner = () => {
  showQRScanner.value = true
  manualQRInput.value = ''
}

const closeQRScanner = () => {
  showQRScanner.value = false
  manualQRInput.value = ''
}

const processQRCode = (qrData) => {
  if (!qrData) {
    alert('Silakan masukkan QR Code terlebih dahulu!')
    return
  }
  
  // Parse QR Code format: IN/20250918/0001|RM-001 (simplified format)
  const qrParts = qrData.split('|')
  if (qrParts.length < 2) {
    alert('Format QR Code tidak valid! Format yang benar: SHIPMENT_NUMBER|KODE_ITEM')
    return
  }
  
  const [shipmentNumber, kodeItem] = qrParts
  
  console.log('Searching for:', { shipmentNumber, kodeItem }) // Debug log
  console.log('Available items:', itemsToQC.value) // Debug log
  
  // Find item by shipment number and kode item
  const foundItem = itemsToQC.value.find(item => 
    item.shipmentNumber.trim() === shipmentNumber.trim() && 
    item.kodeItem.trim() === kodeItem.trim()
  )
  
  if (foundItem) {
    console.log('Item found:', foundItem) // Debug log
    closeQRScanner()
    showItemDetail(foundItem)
  } else {
    alert(`Item tidak ditemukan dalam daftar QC!\nYang dicari: ${shipmentNumber} | ${kodeItem}\n\nItem yang tersedia:\n${itemsToQC.value.map(item => `${item.shipmentNumber} | ${item.kodeItem}`).join('\n')}`)
  }
}

// Action handlers
const printGRSlip = (item) => {
  // Create Good Receipt Slip
  const printWindow = window.open('', '_blank')
  
  printWindow.document.write(`
    <html>
      <head>
        <title>Good Receipt Slip - ${item.kodeItem}</title>
        <style>
          body { font-family: Arial, sans-serif; margin: 20px; }
          .header { text-align: center; border-bottom: 2px solid #000; padding-bottom: 10px; margin-bottom: 20px; }
          .content { margin: 20px 0; }
          .info-row { display: flex; justify-content: space-between; margin: 10px 0; }
          .signature { margin-top: 40px; display: flex; justify-content: space-between; }
          .signature div { text-align: center; }
          .signature-line { border-top: 1px solid #000; width: 150px; margin-top: 40px; }
          @media print {
            body { margin: 0; }
          }
        </style>
      </head>
      <body>
        <div class="header">
          <h2>GOOD RECEIPT SLIP</h2>
          <p>No: GR/${new Date().toISOString().slice(0, 10).replace(/-/g, '')}/${String(item.id).padStart(4, '0')}</p>
        </div>
        
        <div class="content">
          <div class="info-row">
            <span><strong>No PO:</strong> ${item.noPo}</span>
            <span><strong>Tanggal:</strong> ${new Date().toLocaleDateString('id-ID')}</span>
          </div>
          <div class="info-row">
            <span><strong>No Surat Jalan:</strong> ${item.noSuratJalan}</span>
            <span><strong>No Kendaraan:</strong> ${item.noKendaraan}</span>
          </div>
          <div class="info-row">
            <span><strong>Supplier:</strong> ${item.supplier}</span>
            <span><strong>Driver:</strong> ${item.namaDriver}</span>
          </div>
          
          <table style="width: 100%; border-collapse: collapse; margin: 20px 0;">
            <tr style="background-color: #f0f0f0;">
              <th style="border: 1px solid #000; padding: 8px;">Kode Item</th>
              <th style="border: 1px solid #000; padding: 8px;">Nama Material</th>
              <th style="border: 1px solid #000; padding: 8px;">Quantity</th>
              <th style="border: 1px solid #000; padding: 8px;">UoM</th>
              <th style="border: 1px solid #000; padding: 8px;">Status</th>
            </tr>
            <tr>
              <td style="border: 1px solid #000; padding: 8px;">${item.kodeItem}</td>
              <td style="border: 1px solid #000; padding: 8px;">${item.namaMaterial}</td>
              <td style="border: 1px solid #000; padding: 8px; text-align: center;">${item.qtyReceived}</td>
              <td style="border: 1px solid #000; padding: 8px; text-align: center;">${item.uom}</td>
              <td style="border: 1px solid #000; padding: 8px; text-align: center; color: green; font-weight: bold;">PASS</td>
            </tr>
          </table>
        </div>
        
        <div class="signature">
          <div>
            <p>Received By:</p>
            <div class="signature-line"></div>
            <p>Warehouse Staff</p>
          </div>
          <div>
            <p>QC By:</p>
            <div class="signature-line"></div>
            <p>Quality Control</p>
          </div>
          <div>
            <p>Approved By:</p>
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

const printReturnSlip = (item) => {
  // Create Return Slip
  const printWindow = window.open('', '_blank')
  
  printWindow.document.write(`
    <html>
      <head>
        <title>Return Slip - ${item.kodeItem}</title>
        <style>
          body { font-family: Arial, sans-serif; margin: 20px; }
          .header { text-align: center; border-bottom: 2px solid #000; padding-bottom: 10px; margin-bottom: 20px; background-color: #ffe6e6; }
          .content { margin: 20px 0; }
          .info-row { display: flex; justify-content: space-between; margin: 10px 0; }
          .signature { margin-top: 40px; display: flex; justify-content: space-between; }
          .signature div { text-align: center; }
          .signature-line { border-top: 1px solid #000; width: 150px; margin-top: 40px; }
          .reject-note { background-color: #ffe6e6; border: 1px solid #ff0000; padding: 10px; margin: 20px 0; }
          @media print {
            body { margin: 0; }
          }
        </style>
      </head>
      <body>
        <div class="header">
          <h2>RETURN SLIP</h2>
          <p>No: RTN/${new Date().toISOString().slice(0, 10).replace(/-/g, '')}/${String(item.id).padStart(4, '0')}</p>
        </div>
        
        <div class="content">
          <div class="info-row">
            <span><strong>No PO:</strong> ${item.noPo}</span>
            <span><strong>Tanggal:</strong> ${new Date().toLocaleDateString('id-ID')}</span>
          </div>
          <div class="info-row">
            <span><strong>No Surat Jalan:</strong> ${item.noSuratJalan}</span>
            <span><strong>No Kendaraan:</strong> ${item.noKendaraan}</span>
          </div>
          <div class="info-row">
            <span><strong>Supplier:</strong> ${item.supplier}</span>
            <span><strong>Driver:</strong> ${item.namaDriver}</span>
          </div>
          
          <table style="width: 100%; border-collapse: collapse; margin: 20px 0;">
            <tr style="background-color: #f0f0f0;">
              <th style="border: 1px solid #000; padding: 8px;">Kode Item</th>
              <th style="border: 1px solid #000; padding: 8px;">Nama Material</th>
              <th style="border: 1px solid #000; padding: 8px;">Quantity</th>
              <th style="border: 1px solid #000; padding: 8px;">UoM</th>
              <th style="border: 1px solid #000; padding: 8px;">Status</th>
            </tr>
            <tr>
              <td style="border: 1px solid #000; padding: 8px;">${item.kodeItem}</td>
              <td style="border: 1px solid #000; padding: 8px;">${item.namaMaterial}</td>
              <td style="border: 1px solid #000; padding: 8px; text-align: center;">${item.qtyReceived}</td>
              <td style="border: 1px solid #000; padding: 8px; text-align: center;">${item.uom}</td>
              <td style="border: 1px solid #000; padding: 8px; text-align: center; color: red; font-weight: bold;">REJECT</td>
            </tr>
          </table>
          
          <div class="reject-note">
            <strong>ALASAN REJECT:</strong><br>
            Material tidak memenuhi standar QC. Barang akan dikembalikan ke supplier.
          </div>
        </div>
        
        <div class="signature">
          <div>
            <p>QC By:</p>
            <div class="signature-line"></div>
            <p>Quality Control</p>
          </div>
          <div>
            <p>Approved By:</p>
            <div class="signature-line"></div>
            <p>Supervisor</p>
          </div>
          <div>
            <p>Supplier Sign:</p>
            <div class="signature-line"></div>
            <p>Supplier/Driver</p>
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

const printQRLabel = (item) => {
  // Create QR Label
  const printWindow = window.open('', '_blank')
  
  // Generate QR content based on status
  const qrContent = `${item.shipmentNumber}|${item.kodeItem}|${item.statusQC}|${item.qtyReceived}|${new Date().toISOString().slice(0, 10)}`
  
  printWindow.document.write(`
    <html>
      <head>
        <title>QR Label - ${item.kodeItem}</title>
        <style>
          body { font-family: Arial, sans-serif; margin: 20px; }
          .label { 
            border: 2px solid #000; 
            padding: 15px; 
            width: 300px; 
            text-align: center; 
            ${item.statusQC === 'PASS' ? 'background-color: #e6ffe6;' : 'background-color: #ffe6e6;'}
          }
          .qr-placeholder { 
            width: 120px; 
            height: 120px; 
            border: 2px solid #000; 
            margin: 10px auto; 
            display: flex; 
            align-items: center; 
            justify-content: center; 
            background-color: white;
            font-size: 12px;
            text-align: center;
          }
          .item-info { font-size: 12px; margin: 5px 0; }
          .item-code { font-weight: bold; font-size: 14px; }
          .status { 
            font-weight: bold; 
            font-size: 16px; 
            padding: 5px; 
            ${item.statusQC === 'PASS' ? 'color: green;' : 'color: red;'}
          }
          @media print {
            body { margin: 0; }
            .label { page-break-after: always; }
          }
        </style>
      </head>
      <body>
        <div class="label">
          <div class="item-code">${item.kodeItem}</div>
          <div class="item-info">${item.namaMaterial}</div>
          <div class="qr-placeholder">QR CODE<br>${qrContent}</div>
          <div class="status">${item.statusQC === 'PASS' ? 'KARANTINA' : 'REJECT'}</div>
          <div class="item-info">Qty: ${item.qtyReceived} ${item.uom}</div>
          <div class="item-info">PO: ${item.noPo}</div>
          <div class="item-info">Date: ${new Date().toLocaleDateString('id-ID')}</div>
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