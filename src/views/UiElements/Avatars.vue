<template>
  <AdminLayout>
    <PageBreadcrumb :pageTitle="currentPageTitle" />
    <div className="space-y-5 sm:space-y-6">
      <Avatar title="Default Avatar">
      </Avatar>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import Avatar from '@/components/ui/Avatar.vue'
const currentPageTitle = ref('Avatars')


const sizes = ['xsmall', 'small', 'medium', 'large', 'xlarge', 'xxlarge'] as const
</script>
