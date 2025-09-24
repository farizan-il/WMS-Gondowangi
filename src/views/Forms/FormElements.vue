<template>
  <AdminLayout>
    <PageBreadcrumb :pageTitle="currentPageTitle" />
    <div class="grid grid-cols-1 gap-6 w-full">
      <div class="space-y-12 w-full">
        <ComponentCard title="Default Inputs">
          <DefaultInputs />
        </ComponentCard>
      </div>
    </div>
  </AdminLayout>
</template>


<script setup>

import { ref } from 'vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import DefaultInputs from '@/components/forms/FormElements/DefaultInputs.vue'

const currentPageTitle = ref('Form Elements')
</script>
