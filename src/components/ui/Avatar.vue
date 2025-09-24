<template>
  <div class="min-h-screen bg-gray-50 p-6">
    <!-- Header -->
    <div class="mb-6">
      <div class="flex justify-between items-center">
        <div>
          <h1 class="text-3xl font-bold text-gray-900">Kelola Pengguna</h1>
          <p class="text-gray-600 mt-1">Manajemen user, role, dan hak akses sistem</p>
        </div>
        <button 
          @click="showAddModal = true"
          class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 transition-colors"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
          </svg>
          Tambah Pengguna
        </button>
      </div>
    </div>

    <!-- Statistics Cards -->
    <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-6">
      <div class="bg-white rounded-lg shadow-sm p-6">
        <div class="flex items-center">
          <div class="flex-shrink-0">
            <div class="w-8 h-8 bg-blue-500 rounded-full flex items-center justify-center">
              <svg class="w-5 h-5 text-white" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9 6a3 3 0 11-6 0 3 3 0 016 0zM17 6a3 3 0 11-6 0 3 3 0 016 0zM12.93 17c.046-.327.07-.66.07-1a6.97 6.97 0 00-1.5-4.33A5 5 0 0119 16v1h-6.07zM6 11a5 5 0 015 5v1H1v-1a5 5 0 015-5z" />
              </svg>
            </div>
          </div>
          <div class="ml-3">
            <p class="text-sm font-medium text-gray-500">Total Users</p>
            <p class="text-2xl font-semibold text-gray-900">{{ totalUsers }}</p>
          </div>
        </div>
      </div>
      <div class="bg-white rounded-lg shadow-sm p-6">
        <div class="flex items-center">
          <div class="flex-shrink-0">
            <div class="w-8 h-8 bg-green-500 rounded-full flex items-center justify-center">
              <svg class="w-5 h-5 text-white" fill="currentColor" viewBox="0 0 20 20">
                <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
              </svg>
            </div>
          </div>
          <div class="ml-3">
            <p class="text-sm font-medium text-gray-500">Active Users</p>
            <p class="text-2xl font-semibold text-gray-900">{{ activeUsers }}</p>
          </div>
        </div>
      </div>
      <div class="bg-white rounded-lg shadow-sm p-6">
        <div class="flex items-center">
          <div class="flex-shrink-0">
            <div class="w-8 h-8 bg-yellow-500 rounded-full flex items-center justify-center">
              <svg class="w-5 h-5 text-white" fill="currentColor" viewBox="0 0 20 20">
                <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-8-3a1 1 0 00-.867.5 1 1 0 11-1.731-1A3 3 0 0113 8a3.001 3.001 0 01-2 2.83V11a1 1 0 11-2 0v-1a1 1 0 011-1 1 1 0 100-2zm0 8a1 1 0 100-2 1 1 0 000 2z" clip-rule="evenodd" />
              </svg>
            </div>
          </div>
          <div class="ml-3">
            <p class="text-sm font-medium text-gray-500">Inactive Users</p>
            <p class="text-2xl font-semibold text-gray-900">{{ inactiveUsers }}</p>
          </div>
        </div>
      </div>
      <div class="bg-white rounded-lg shadow-sm p-6">
        <div class="flex items-center">
          <div class="flex-shrink-0">
            <div class="w-8 h-8 bg-purple-500 rounded-full flex items-center justify-center">
              <svg class="w-5 h-5 text-white" fill="currentColor" viewBox="0 0 20 20">
                <path d="M13 6a3 3 0 11-6 0 3 3 0 016 0zM18 8a2 2 0 11-4 0 2 2 0 014 0zM14 15a4 4 0 00-8 0v3h8v-3z" />
              </svg>
            </div>
          </div>
          <div class="ml-3">
            <p class="text-sm font-medium text-gray-500">Admin Users</p>
            <p class="text-2xl font-semibold text-gray-900">{{ adminUsers }}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Filter dan Search -->
    <div class="bg-white rounded-lg shadow-sm p-4 mb-6">
      <div class="flex flex-wrap gap-4">
        <div class="flex-1 min-w-64">
          <input 
            v-model="searchQuery"
            type="text" 
            placeholder="Cari username, nama lengkap, NIK..."
            class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          >
        </div>
        <select v-model="departmentFilter" class="px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
          <option value="">Semua Departemen</option>
          <option value="Gudang">Gudang</option>
          <option value="QC">Quality Control</option>
          <option value="Produksi">Produksi</option>
          <option value="PPIC">PPIC</option>
          <option value="IT">IT</option>
          <option value="HRD">HRD</option>
        </select>
        <select v-model="roleFilter" class="px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
          <option value="">Semua Role</option>
          <option value="Admin">Admin</option>
          <option value="Receiving">Receiving</option>
          <option value="QC">QC</option>
          <option value="Warehouse">Warehouse</option>
          <option value="Production">Production</option>
          <option value="Supervisor">Supervisor</option>
        </select>
        <select v-model="statusFilter" class="px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500">
          <option value="">Semua Status</option>
          <option value="Active">Active</option>
          <option value="Inactive">Inactive</option>
        </select>
      </div>
    </div>

    <!-- Tabel Users -->
    <div class="bg-white rounded-lg shadow-sm overflow-hidden">
      <div class="overflow-x-auto">
        <table class="min-w-full divide-y divide-gray-200">
          <thead class="bg-gray-50">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Username</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Nama Lengkap</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">NIK</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Jabatan</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Departemen</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Role</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Last Login</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Aksi</th>
            </tr>
          </thead>
          <tbody class="bg-white divide-y divide-gray-200">
            <tr v-for="user in filteredUsers" :key="user.id" class="hover:bg-gray-50">
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="flex items-center">
                  <div class="flex-shrink-0 h-10 w-10">
                    <div class="h-10 w-10 rounded-full bg-gray-200 flex items-center justify-center">
                      <span class="text-sm font-medium text-gray-700">{{ user.fullName.split(' ').map(n => n[0]).join('').substring(0, 2).toUpperCase() }}</span>
                    </div>
                  </div>
                  <div class="ml-4">
                    <div class="text-sm font-medium text-gray-900">{{ user.username }}</div>
                    <div class="text-sm text-gray-500">{{ user.email || '-' }}</div>
                  </div>
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-gray-900">{{ user.fullName }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm font-mono text-gray-900">{{ user.nik }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-gray-900">{{ user.jabatan }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-gray-900">{{ user.department }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span :class="getRoleClass(user.role)" class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full">
                  {{ user.role }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <button
                  @click="toggleUserStatus(user)"
                  :class="user.status === 'Active' ? 'bg-green-100 text-green-800' : 'bg-red-100 text-red-800'"
                  class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full cursor-pointer transition-colors hover:opacity-80"
                >
                  {{ user.status }}
                </button>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-gray-900">{{ user.lastLogin ? formatDate(user.lastLogin) : 'Belum Login' }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium space-x-2">
                <button 
                  @click="editUser(user)"
                  class="text-blue-600 hover:text-blue-900 bg-blue-50 hover:bg-blue-100 px-2 py-1 rounded transition-colors"
                >
                  Edit
                </button>
                <button 
                  @click="resetPassword(user)"
                  class="text-yellow-600 hover:text-yellow-900 bg-yellow-50 hover:bg-yellow-100 px-2 py-1 rounded transition-colors"
                >
                  Reset
                </button>
                <button 
                  @click="deleteUser(user)"
                  :disabled="user.role === 'Admin' && adminUsers <= 1"
                  :class="user.role === 'Admin' && adminUsers <= 1 
                    ? 'text-gray-400 cursor-not-allowed bg-gray-50' 
                    : 'text-red-600 hover:text-red-900 bg-red-50 hover:bg-red-100'"
                  class="px-2 py-1 rounded transition-colors"
                >
                  Hapus
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Modal Add/Edit User -->
    <div v-if="showAddModal || showEditModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg p-6 w-full max-w-2xl max-h-screen overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
          <h3 class="text-xl font-semibold">{{ showEditModal ? 'Edit Pengguna' : 'Tambah Pengguna Baru' }}</h3>
          <button 
            @click="closeModal"
            class="text-gray-400 hover:text-gray-600"
          >
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- NIK -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">NIK <span class="text-red-500">*</span></label>
            <input 
              v-model="formData.nik"
              :disabled="showEditModal"
              type="text" 
              maxlength="6"
              placeholder="6 digit unik"
              :class="showEditModal ? 'bg-gray-100 cursor-not-allowed' : ''"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            >
            <p v-if="nikError" class="text-red-500 text-xs mt-1">{{ nikError }}</p>
          </div>

          <!-- Username -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Username <span class="text-red-500">*</span></label>
            <div class="flex gap-2">
              <input 
                v-model="formData.username"
                :disabled="showEditModal"
                type="text" 
                placeholder="Username unik"
                :class="showEditModal ? 'bg-gray-100 cursor-not-allowed' : ''"
                class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              >
              <button 
                v-if="!showEditModal"
                @click="generateUsername"
                type="button"
                class="px-3 py-2 bg-gray-500 text-white rounded-lg hover:bg-gray-600 transition-colors text-sm"
              >
                Auto
              </button>
            </div>
            <p v-if="usernameError" class="text-red-500 text-xs mt-1">{{ usernameError }}</p>
          </div>

          <!-- Nama Lengkap -->
          <div class="md:col-span-2">
            <label class="block text-sm font-medium text-gray-700 mb-1">Nama Lengkap <span class="text-red-500">*</span></label>
            <input 
              v-model="formData.fullName"
              type="text" 
              placeholder="Nama lengkap pengguna"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            >
          </div>

          <!-- Email -->
          <div class="md:col-span-2">
            <label class="block text-sm font-medium text-gray-700 mb-1">Email</label>
            <input 
              v-model="formData.email"
              type="email" 
              placeholder="email@company.com (opsional)"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            >
          </div>

          <!-- Password (only for add) -->
          <div v-if="!showEditModal" class="md:col-span-2">
            <label class="block text-sm font-medium text-gray-700 mb-1">Password</label>
            <div class="flex gap-2">
              <input 
                v-model="formData.password"
                :type="showPassword ? 'text' : 'password'"
                placeholder="Password akan di-generate otomatis"
                class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              >
              <button 
                @click="showPassword = !showPassword"
                type="button"
                class="px-3 py-2 bg-gray-500 text-white rounded-lg hover:bg-gray-600 transition-colors"
              >
                {{ showPassword ? '🙈' : '👁️' }}
              </button>
              <button 
                @click="generatePassword"
                type="button"
                class="px-3 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors text-sm"
              >
                Generate
              </button>
            </div>
          </div>

          <!-- Jabatan -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Jabatan <span class="text-red-500">*</span></label>
            <select 
              v-model="formData.jabatan" 
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500"
            >
              <option value="">Pilih Jabatan</option>
              <option value="Staff">Staff</option>
              <option value="Operator">Operator</option>
              <option value="Supervisor">Supervisor</option>
              <option value="Manager">Manager</option>
            </select>
          </div>

          <!-- Departemen -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Departemen <span class="text-red-500">*</span></label>
            <select 
              v-model="formData.department" 
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500"
            >
              <option value="">Pilih Departemen</option>
              <option value="Gudang">Gudang</option>
              <option value="QC">Quality Control</option>
              <option value="Produksi">Produksi</option>
              <option value="PPIC">PPIC</option>
              <option value="IT">IT</option>
              <option value="HRD">HRD</option>
            </select>
          </div>

          <!-- Role -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Role / Hak Akses <span class="text-red-500">*</span></label>
            <select 
              v-model="formData.role" 
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500"
            >
              <option value="">Pilih Role</option>
              <option value="Admin">Admin</option>
              <option value="Receiving">Receiving</option>
              <option value="QC">QC</option>
              <option value="Warehouse">Warehouse</option>
              <option value="Production">Production</option>
              <option value="Supervisor">Supervisor</option>
            </select>
          </div>

          <!-- Status -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Status <span class="text-red-500">*</span></label>
            <select 
              v-model="formData.status" 
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500"
            >
              <option value="Active">Active</option>
              <option value="Inactive">Inactive</option>
            </select>
          </div>
        </div>

        <!-- Actions -->
        <div class="flex justify-end gap-3 mt-6 pt-4 border-t">
          <button 
            @click="closeModal"
            class="px-4 py-2 text-gray-600 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors"
          >
            Batal
          </button>
          <button 
            @click="saveUser"
            :disabled="!canSave"
            :class="canSave 
              ? 'bg-blue-600 hover:bg-blue-700 text-white' 
              : 'bg-gray-300 text-gray-500 cursor-not-allowed'"
            class="px-4 py-2 rounded-lg transition-colors"
          >
            {{ showEditModal ? 'Update' : 'Simpan' }}
          </button>
        </div>
      </div>
    </div>

    <!-- Modal Reset Password -->
    <div v-if="showResetModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <h3 class="text-lg font-semibold mb-4">Reset Password</h3>
        <p class="text-gray-600 mb-4">Apakah Anda yakin ingin mereset password untuk user <strong>{{ resetUser?.username }}</strong>?</p>
        
        <div v-if="newPassword" class="bg-blue-50 border border-blue-200 rounded-lg p-4 mb-4">
          <p class="text-sm text-blue-800 mb-2">Password baru telah di-generate:</p>
          <div class="flex items-center gap-2">
            <code class="flex-1 bg-blue-100 px-3 py-2 rounded font-mono text-sm">{{ newPassword }}</code>
            <button 
              @click="copyPassword"
              class="px-3 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 transition-colors text-sm"
            >
              Copy
            </button>
          </div>
          <p class="text-xs text-blue-600 mt-2">* Pastikan untuk menyimpan password ini dengan aman</p>
        </div>

        <div class="flex justify-end gap-3">
          <button 
            @click="closeResetModal"
            class="px-4 py-2 text-gray-600 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors"
          >
            {{ newPassword ? 'Tutup' : 'Batal' }}
          </button>
          <button 
            v-if="!newPassword"
            @click="confirmResetPassword"
            class="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors"
          >
            Reset Password
          </button>
        </div>
      </div>
    </div>

    <!-- Modal Delete Confirmation -->
    <div v-if="showDeleteModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg p-6 w-full max-w-md">
        <h3 class="text-lg font-semibold mb-4">Hapus Pengguna</h3>
        <p class="text-gray-600 mb-4">
          Apakah Anda yakin ingin menghapus user <strong>{{ deleteUserData?.username }}</strong>?
          <br><br>
          User akan di-nonaktifkan (soft delete) untuk menjaga histori transaksi.
        </p>
        <div class="flex justify-end gap-3">
          <button 
            @click="showDeleteModal = false"
            class="px-4 py-2 text-gray-600 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors"
          >
            Batal
          </button>
          <button 
            @click="confirmDeleteUser"
            class="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors"
          >
            Hapus
          </button>
        </div>
      </div>
    </div>

    <!-- Success/Error Messages -->
    <div v-if="message" :class="message.type === 'success' ? 'bg-green-50 border-green-200 text-green-800' : 'bg-red-50 border-red-200 text-red-800'" class="fixed top-4 right-4 border rounded-lg p-4 shadow-lg z-50">
      <div class="flex items-center gap-2">
        <svg v-if="message.type === 'success'" class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
          <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
        </svg>
        <svg v-else class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
          <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
        </svg>
        {{ message.text }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue'

// Types
interface User {
  id: string
  nik: string
  username: string
  fullName: string
  email?: string
  jabatan: string
  department: string
  role: string
  status: 'Active' | 'Inactive'
  createdAt: string
  lastLogin?: string
}

// Reactive data
const users = ref<User[]>([])
const searchQuery = ref('')
const departmentFilter = ref('')
const roleFilter = ref('')
const statusFilter = ref('')

// Modals
const showAddModal = ref(false)
const showEditModal = ref(false)
const showResetModal = ref(false)
const showDeleteModal = ref(false)

// Form data
const formData = ref({
  nik: '',
  username: '',
  fullName: '',
  email: '',
  password: '',
  jabatan: '',
  department: '',
  role: '',
  status: 'Active'
})

// Other states
const showPassword = ref(false)
const editingUser = ref<User | null>(null)
const resetUser = ref<User | null>(null)
const deleteUserData = ref<User | null>(null)
const newPassword = ref('')
const nikError = ref('')
const usernameError = ref('')
const message = ref<{ type: 'success' | 'error', text: string } | null>(null)

// Computed properties
const filteredUsers = computed(() => {
  return users.value.filter(user => {
    const matchesSearch = !searchQuery.value || 
      user.username.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      user.fullName.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      user.nik.includes(searchQuery.value)
    
    const matchesDepartment = !departmentFilter.value || user.department === departmentFilter.value
    const matchesRole = !roleFilter.value || user.role === roleFilter.value
    const matchesStatus = !statusFilter.value || user.status === statusFilter.value
    
    return matchesSearch && matchesDepartment && matchesRole && matchesStatus
  })
})

const totalUsers = computed(() => users.value.length)
const activeUsers = computed(() => users.value.filter(u => u.status === 'Active').length)
const inactiveUsers = computed(() => users.value.filter(u => u.status === 'Inactive').length)
const adminUsers = computed(() => users.value.filter(u => u.role === 'Admin' && u.status === 'Active').length)

const canSave = computed(() => {
  const requiredFields = ['nik', 'username', 'fullName', 'jabatan', 'department', 'role', 'status']
  const hasAllFields = requiredFields.every(field => formData.value[field as keyof typeof formData.value])
  const hasValidNik = formData.value.nik.length === 6 && !nikError.value
  const hasValidUsername = formData.value.username && !usernameError.value
  const hasPassword = showEditModal.value || formData.value.password
  
  return hasAllFields && hasValidNik && hasValidUsername && hasPassword
})

// Methods
const formatDate = (dateString: string) => {
  return new Date(dateString).toLocaleDateString('id-ID', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

const getRoleClass = (role: string) => {
  const colors: Record<string, string> = {
    'Admin': 'bg-red-100 text-red-800',
    'Receiving': 'bg-blue-100 text-blue-800',
    'QC': 'bg-green-100 text-green-800',
    'Warehouse': 'bg-yellow-100 text-yellow-800',
    'Production': 'bg-purple-100 text-purple-800',
    'Supervisor': 'bg-orange-100 text-orange-800'
  }
  return colors[role] || 'bg-gray-100 text-gray-800'
}

const generateNik = () => {
  let nik: string
  do {
    nik = Math.floor(Math.random() * 900000 + 100000).toString()
  } while (users.value.some(u => u.nik === nik))
  return nik
}

const generateUsername = () => {
  if (!formData.value.fullName) {
    showMessage('error', 'Masukkan nama lengkap terlebih dahulu')
    return
  }
  
  const nameParts = formData.value.fullName.toLowerCase().split(' ')
  let baseUsername = nameParts[0]
  
  if (nameParts.length > 1) {
    baseUsername += nameParts[nameParts.length - 1].charAt(0)
  }
  
  let username = baseUsername
  let counter = 1
  
  while (users.value.some(u => u.username === username)) {
    username = baseUsername + counter
    counter++
  }
  
  formData.value.username = username
}

const generatePassword = () => {
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%'
  let password = ''
  
  // Ensure at least one of each type
  password += 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'[Math.floor(Math.random() * 26)]
  password += 'abcdefghijklmnopqrstuvwxyz'[Math.floor(Math.random() * 26)]
  password += '0123456789'[Math.floor(Math.random() * 10)]
  password += '!@#$%'[Math.floor(Math.random() * 5)]
  
  // Fill the rest
  for (let i = 4; i < 12; i++) {
    password += chars[Math.floor(Math.random() * chars.length)]
  }
  
  // Shuffle the password
  formData.value.password = password.split('').sort(() => 0.5 - Math.random()).join('')
}

const validateNik = () => {
  if (!formData.value.nik) {
    nikError.value = ''
    return
  }
  
  if (formData.value.nik.length !== 6) {
    nikError.value = 'NIK harus 6 digit'
    return
  }
  
  if (!/^\d+$/.test(formData.value.nik)) {
    nikError.value = 'NIK hanya boleh berisi angka'
    return
  }
  
  const isDuplicate = users.value.some(u => 
    u.nik === formData.value.nik && u.id !== editingUser.value?.id
  )
  
  if (isDuplicate) {
    nikError.value = 'NIK sudah digunakan'
    return
  }
  
  nikError.value = ''
}

const validateUsername = () => {
  if (!formData.value.username) {
    usernameError.value = ''
    return
  }
  
  if (formData.value.username.length < 3) {
    usernameError.value = 'Username minimal 3 karakter'
    return
  }
  
  if (!/^[a-zA-Z0-9_]+$/.test(formData.value.username)) {
    usernameError.value = 'Username hanya boleh berisi huruf, angka, dan underscore'
    return
  }
  
  const isDuplicate = users.value.some(u => 
    u.username === formData.value.username && u.id !== editingUser.value?.id
  )
  
  if (isDuplicate) {
    usernameError.value = 'Username sudah digunakan'
    return
  }
  
  usernameError.value = ''
}

const resetForm = () => {
  formData.value = {
    nik: '',
    username: '',
    fullName: '',
    email: '',
    password: '',
    jabatan: '',
    department: '',
    role: '',
    status: 'Active'
  }
  nikError.value = ''
  usernameError.value = ''
  showPassword.value = false
  editingUser.value = null
}

const closeModal = () => {
  showAddModal.value = false
  showEditModal.value = false
  resetForm()
}

const editUser = (user: User) => {
  editingUser.value = user
  formData.value = {
    nik: user.nik,
    username: user.username,
    fullName: user.fullName,
    email: user.email || '',
    password: '', // Don't populate password for security
    jabatan: user.jabatan,
    department: user.department,
    role: user.role,
    status: user.status
  }
  showEditModal.value = true
}

const saveUser = () => {
  if (!canSave.value) return
  
  const userData: User = {
    id: editingUser.value?.id || Date.now().toString(),
    nik: formData.value.nik,
    username: formData.value.username,
    fullName: formData.value.fullName,
    email: formData.value.email || undefined,
    jabatan: formData.value.jabatan,
    department: formData.value.department,
    role: formData.value.role,
    status: formData.value.status as 'Active' | 'Inactive',
    createdAt: editingUser.value?.createdAt || new Date().toISOString(),
    lastLogin: editingUser.value?.lastLogin
  }
  
  if (editingUser.value) {
    // Update existing user
    const index = users.value.findIndex(u => u.id === editingUser.value!.id)
    if (index !== -1) {
      users.value[index] = userData
      showMessage('success', `User ${userData.username} berhasil diupdate`)
    }
  } else {
    // Add new user
    users.value.unshift(userData)
    showMessage('success', `User ${userData.username} berhasil ditambahkan`)
    
    // Show generated password
    if (formData.value.password) {
      setTimeout(() => {
        showMessage('success', `Password: ${formData.value.password} (simpan dengan aman!)`)
      }, 2000)
    }
  }
  
  closeModal()
}

const toggleUserStatus = (user: User) => {
  // Prevent deactivating the last admin
  if (user.role === 'Admin' && user.status === 'Active' && adminUsers.value <= 1) {
    showMessage('error', 'Tidak dapat menonaktifkan admin terakhir')
    return
  }
  
  const newStatus = user.status === 'Active' ? 'Inactive' : 'Active'
  user.status = newStatus
  
  showMessage('success', `Status user ${user.username} berubah menjadi ${newStatus}`)
}

const resetPassword = (user: User) => {
  resetUser.value = user
  newPassword.value = ''
  showResetModal.value = true
}

const confirmResetPassword = () => {
  if (!resetUser.value) return
  
  // Generate new password
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%'
  let password = ''
  
  // Ensure at least one of each type
  password += 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'[Math.floor(Math.random() * 26)]
  password += 'abcdefghijklmnopqrstuvwxyz'[Math.floor(Math.random() * 26)]
  password += '0123456789'[Math.floor(Math.random() * 10)]
  password += '!@#$%'[Math.floor(Math.random() * 5)]
  
  // Fill the rest
  for (let i = 4; i < 10; i++) {
    password += chars[Math.floor(Math.random() * chars.length)]
  }
  
  // Shuffle the password
  newPassword.value = password.split('').sort(() => 0.5 - Math.random()).join('')
  
  showMessage('success', `Password berhasil direset untuk user ${resetUser.value.username}`)
}

const copyPassword = () => {
  navigator.clipboard.writeText(newPassword.value).then(() => {
    showMessage('success', 'Password berhasil disalin')
  })
}

const closeResetModal = () => {
  showResetModal.value = false
  resetUser.value = null
  newPassword.value = ''
}

const deleteUser = (user: User) => {
  // Prevent deleting the last admin
  if (user.role === 'Admin' && adminUsers.value <= 1) {
    showMessage('error', 'Tidak dapat menghapus admin terakhir')
    return
  }
  
  deleteUserData.value = user
  showDeleteModal.value = true
}

const confirmDeleteUser = () => {
  if (!deleteUserData.value) return
  
  // Soft delete - just change status to Inactive
  deleteUserData.value.status = 'Inactive'
  
  showMessage('success', `User ${deleteUserData.value.username} berhasil dihapus (dinonaktifkan)`)
  showDeleteModal.value = false
  deleteUserData.value = null
}

const showMessage = (type: 'success' | 'error', text: string) => {
  message.value = { type, text }
  setTimeout(() => {
    message.value = null
  }, 4000)
}

// Watchers
watch(() => formData.value.nik, validateNik)
watch(() => formData.value.username, validateUsername)

// Initialize dummy data
onMounted(() => {
  users.value = [
    {
      id: '1',
      nik: '123456',
      username: 'admin',
      fullName: 'Administrator System',
      email: 'admin@company.com',
      jabatan: 'Manager',
      department: 'IT',
      role: 'Admin',
      status: 'Active',
      createdAt: '2024-01-15T08:00:00Z',
      lastLogin: '2024-09-19T14:30:00Z'
    },
    {
      id: '2',
      nik: '234567',
      username: 'johndoe',
      fullName: 'John Doe',
      email: 'john.doe@company.com',
      jabatan: 'Staff',
      department: 'Gudang',
      role: 'Warehouse',
      status: 'Active',
      createdAt: '2024-02-10T09:00:00Z',
      lastLogin: '2024-09-19T08:15:00Z'
    },
    {
      id: '3',
      nik: '345678',
      username: 'marys',
      fullName: 'Mary Smith',
      email: 'mary.smith@company.com',
      jabatan: 'Operator',
      department: 'QC',
      role: 'QC',
      status: 'Active',
      createdAt: '2024-02-20T10:30:00Z',
      lastLogin: '2024-09-18T16:45:00Z'
    },
    {
      id: '4',
      nik: '456789',
      username: 'bobw',
      fullName: 'Bob Wilson',
      email: 'bob.wilson@company.com',
      jabatan: 'Staff',
      department: 'Gudang',
      role: 'Receiving',
      status: 'Active',
      createdAt: '2024-03-05T11:00:00Z',
      lastLogin: '2024-09-19T07:30:00Z'
    },
    {
      id: '5',
      nik: '567890',
      username: 'aliceb',
      fullName: 'Alice Brown',
      email: 'alice.brown@company.com',
      jabatan: 'Supervisor',
      department: 'Produksi',
      role: 'Production',
      status: 'Active',
      createdAt: '2024-03-15T12:00:00Z',
      lastLogin: '2024-09-19T09:00:00Z'
    },
    {
      id: '6',
      nik: '678901',
      username: 'tomj',
      fullName: 'Tom Johnson',
      email: 'tom.johnson@company.com',
      jabatan: 'Manager',
      department: 'PPIC',
      role: 'Supervisor',
      status: 'Active',
      createdAt: '2024-04-01T13:30:00Z',
      lastLogin: '2024-09-18T17:20:00Z'
    },
    {
      id: '7',
      nik: '789012',
      username: 'sarahd',
      fullName: 'Sarah Davis',
      email: 'sarah.davis@company.com',
      jabatan: 'Staff',
      department: 'HRD',
      role: 'Supervisor',
      status: 'Inactive',
      createdAt: '2024-04-15T14:00:00Z'
    },
    {
      id: '8',
      nik: '890123',
      username: 'mikec',
      fullName: 'Mike Chen',
      email: 'mike.chen@company.com',
      jabatan: 'Operator',
      department: 'Gudang',
      role: 'Warehouse',
      status: 'Active',
      createdAt: '2024-05-01T15:30:00Z',
      lastLogin: '2024-09-19T10:45:00Z'
    },
    {
      id: '9',
      nik: '901234',
      username: 'lisak',
      fullName: 'Lisa Kim',
      jabatan: 'Staff',
      department: 'QC',
      role: 'QC',
      status: 'Active',
      createdAt: '2024-05-20T16:00:00Z',
      lastLogin: '2024-09-19T11:30:00Z'
    },
    {
      id: '10',
      nik: '012345',
      username: 'davidl',
      fullName: 'David Lee',
      email: 'david.lee@company.com',
      jabatan: 'Supervisor',
      department: 'IT',
      role: 'Admin',
      status: 'Active',
      createdAt: '2024-06-01T17:30:00Z',
      lastLogin: '2024-09-19T12:00:00Z'
    }
  ]
  
  // Auto-generate password for new user demo
  generatePassword()
})
</script>

