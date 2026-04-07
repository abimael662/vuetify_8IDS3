<!-- src/layouts/ProtectedLayout.vue -->
<template>
  <div v-if="tokenExists">
    <slot />
  </div>
  <div v-else>
    <Login />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import Login from '@/components/Login.vue'

const tokenExists = ref(!!localStorage.getItem('token'))
</script>