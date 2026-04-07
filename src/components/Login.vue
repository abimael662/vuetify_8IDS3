<template>
    <div>
        <v-card class="mx-auto my-6 pa-12 pb-8" elevation="3" max-width="448" rounded="lg">
            <div class="text-body-large text-medium-emphasis">Account</div>
            <!-- ALERTA ROJA -->
            <v-alert type="error" v-if="errorMessage" variant="tonal" class="mb-4">
                {{ errorMessage }}
            </v-alert>

            <!-- EMAIL -->
            <v-text-field density="compact" placeholder=" address" prepend-inner-icon="mdi-email-outline"
                v-model="username" :rules="[
                    v => !!v || 'This field is required',
                    v => /.+@.+\..+/.test(v) || 'Correo inválido'
                ]" type="email" variant="outlined"></v-text-field>

            <!-- PASSWORD -->
            <div class="text-body-large text-medium-emphasis d-flex align-center justify-space-between">
                Password
                <a class="text-body-small text-decoration-none text-blue" href="#" target="_blank">
                    Forgot login password?
                </a>
            </div>

            <v-text-field v-model="password" :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
                :type="visible ? 'text' : 'password'" :rules="[() => !!password || 'This field is required']"
                density="compact" placeholder="Enter your password" prepend-inner-icon="mdi-lock-outline"
                variant="outlined" @click:append-inner="visible = !visible"></v-text-field>

            <!-- BOTÓN -->
            <v-btn class="mb-8" color="blue" size="large" variant="tonal" block @click="login" :loading="loading">
                Log In
            </v-btn>

            <v-card-text class="text-center">
                <v-btn to="/signup" variant="plain" class="text-blue text-decoration-none">
                    Sign Up now <v-icon icon="mdi-chevron-right"></v-icon>
                </v-btn>
            </v-card-text>
        </v-card>
    </div>
</template>

<script lang="ts" setup>
import axios from "@/config/axios";
import { ref } from "vue";
import { useRouter } from "vue-router";

const username = ref('')
const password = ref('')
const visible = ref(false)
const loading = ref(false)
const router = useRouter()
const errorMessage = ref('')

const login = async () => {
    try {
        loading.value = true
        errorMessage.value = ""
        if (!username.value || !password.value) {
            errorMessage.value = "Ingresar todos los datos"
            loading.value = false
            return
        }

        const response = await axios.post('login', {
            email: username.value,
            password: password.value
        })
        console.log(response)

        //if (response.data.access == 'Ok') {
        if (response.data.token) {
            localStorage.setItem("token", response.data.token)
            // localStorage.setItem("role", response.data.role) 
            await router.push('/home')
        }
        else {
            errorMessage.value = response.data.error
        }
    } catch (error) {
        errorMessage.value = "Ocurrió un error en el servidor"
        console.log(error)
    }
    loading.value = false
}
</script>