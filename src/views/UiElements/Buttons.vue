<template>
  <AdminLayout>
    <div class="space-y-5 sm:space-y-6">
      <ComponentCard title="Primary Button with Right Icon">
        <div class="flex items-center gap-5">
          <Button size="md" variant="outline" :endIcon="BoxIcon"> Button Text </Button>
        </div>
      </ComponentCard>
    </div>
  </AdminLayout>
</template>

<script setup>
import { ref } from 'vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import Button from '@/components/ui/Button.vue'
import { BoxIcon } from '@/icons'
const currentPageTitle = ref('Buttons')
</script>

<style></style>
