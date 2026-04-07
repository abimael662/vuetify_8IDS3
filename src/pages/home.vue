<template>
  <Dashboard />
</template>

<script lang="ts" setup>
import Dashboard from '@/components/Dashboard.vue'
definePage({
  meta: {
    requiresAuth: true
  }
})
</script>