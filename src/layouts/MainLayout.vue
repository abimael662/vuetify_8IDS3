<template>
  <v-app>
    <!-- Sidebar -->
    <v-navigation-drawer v-model="drawer" app>
      <v-list>
        <v-list-item title="Dashboard" prepend-icon="mdi-view-dashboard" to="/home" />
        <v-list-item title="Productos" prepend-icon="mdi-cart" to="/producto" />
        <v-list-item title="Cerrar sesión" prepend-icon="mdi-logout" @click="logout" />
      </v-list>
    </v-navigation-drawer>

    <!-- Navbar -->
    <v-app-bar app color="primary" dark>
      <v-app-bar-nav-icon @click="drawer = !drawer" />
      <v-toolbar-title>Mi Hola</v-toolbar-title>
    </v-app-bar>

    <!-- Aquí cambia el contenido -->
    <v-main>
      <!-- <router-view /> -->
      <slot />
    </v-main>
  </v-app>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
const drawer = ref(false)
const router = useRouter()

const logout = () => {
  localStorage.removeItem('token')
  drawer.value = false
  router.push('/')
}
</script>