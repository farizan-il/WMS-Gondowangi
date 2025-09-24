<template>
  <AdminLayout>
    <div class="space-y-5 sm:space-y-6">
      <badge title="With Light Background">
        <PageBreadcrumb />
      </badge>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import PageBreadcrumb from '../../components/common/PageBreadcrumb.vue'
import AdminLayout from '../../components/layout/AdminLayout.vue'
import { PlusIcon } from '@/icons'
const currentPageTitle = ref('Badge')

const colors = ['primary', 'success', 'error', 'warning', 'info', 'light', 'dark'] as const
</script>
