<template>
  <Crud />
</template>

<script lang="ts" setup>
import Crud from '@/components/Productos.vue'
definePage({
  meta: {
    requiresAuth: true
  }
})
</script>