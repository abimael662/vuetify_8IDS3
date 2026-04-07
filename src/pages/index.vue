<template>
  <div v-if="tokenExists">
    <Dashboard />
  </div>
  <div v-else>
    <Login />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import Dashboard from '@/components/Dashboard.vue'
import Login from '@/components/Login.vue'

const tokenExists = computed(() => !!localStorage.getItem('token'))
</script>
<!-- <template>
  <Login />
</template>

<script lang="ts" setup>
  import Login from '@/components/Login.vue'
</script> -->