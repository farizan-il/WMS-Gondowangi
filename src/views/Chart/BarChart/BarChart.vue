<template>
  <AdminLayout>
    <BarChartOne :pageTitle="currentPageTitle" />
  </AdminLayout>
</template>

<script setup>
import BarChartOne from '@/components/charts/BarChart/BarChartOne.vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { ref } from 'vue'
const currentPageTitle = ref('Bar Chart')
</script>
