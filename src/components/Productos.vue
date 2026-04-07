<template>
    <MainLayout>
        <v-container>
            <v-sheet border rounded>
                <v-data-table :loading="loading" :headers="headers" :hide-default-footer="productos.length < 11"
                    :items="productos" :search="search"  v-model:items-per-page="itemsPerPage">
                    <template v-slot:top>
                        <v-toolbar flat>
                            <v-toolbar-title>
                                Productos
                            </v-toolbar-title>
                            <v-text-field v-model="search" class="ma-2" density="compact" placeholder="Search name..." hide-details></v-text-field>

                            <v-btn class="me-2" prepend-icon="mdi-plus" rounded="lg" text="Add a Product" border
                                @click="add"></v-btn>
                        </v-toolbar>
                    </template>

                    <template v-slot:item.actions="{ item }">
                        <div class="d-flex ga-2 justify-end">
                            <v-icon color="medium-emphasis" icon="mdi-pencil" size="small"
                                @click="edit(item.id)"></v-icon>

                            <v-icon color="medium-emphasis" icon="mdi-delete" size="small"
                                @click="deleteProducto(item.id)"></v-icon>
                        </div>
                    </template>

                    <template v-slot:no-data>
                        <v-btn prepend-icon="mdi-backup-restore" rounded="lg" text="Reset data" variant="text" border
                            @click="reset"></v-btn>
                    </template>
                </v-data-table>
            </v-sheet>

            <v-dialog v-model="dialog" max-width="500">
                <v-card>
                    <template v-slot:text>
                        <v-card-title>
                            {{ isEditing ? 'Editar Producto' : 'Agregar Producto' }}
                        </v-card-title>

                        <v-card-subtitle>
                            {{ isEditing ? 'Actualiza tu producto' : 'Crea un nuevo producto' }}
                        </v-card-subtitle>
                        <v-row>
                            <v-col cols="12">
                                <v-text-field v-model="formModel.codigo" label="Codigo"></v-text-field>
                            </v-col>

                            <v-col cols="12" md="12">
                                <v-textarea v-model="formModel.descripcion" label="Descripcion"></v-textarea>
                            </v-col>

                            <v-col cols="12" md="6">
                                <v-number-input v-model="formModel.precio" :min="0" :step="0.01" label="Precio"
                                    :precision="2"></v-number-input>
                            </v-col>

                            <v-col cols="12" md="6">
                                <v-number-input v-model="formModel.impuesto" :min="0" :step="0.01" label="Impuesto"
                                   :precision="2"></v-number-input>
                            </v-col>
                        </v-row>
                    </template>

                    <v-divider></v-divider>

                    <v-card-actions class="bg-surface-light">
                        <v-btn text="Cancel" variant="plain" @click="dialog = false"></v-btn>

                        <v-spacer></v-spacer>

                        <v-btn text="Save" @click="save"></v-btn>
                    </v-card-actions>
                </v-card>
            </v-dialog>
        </v-container>
    </MainLayout>
</template>
<script  setup>
import MainLayout from '@/layouts/MainLayout.vue'
import axios from '@/config/axios'
import { ref, shallowRef, onMounted, computed } from 'vue'

function createNewRecord() {
    return {
        id: null,
        codigo: '',
        descripcion: '',
        precio: 0,
        impuesto: 0,
    }
}

const productos = ref([])
const formModel = ref(createNewRecord())
const loading = ref(false)
const dialog = shallowRef(false)
const isEditing = computed(() => !!formModel.value.id)
const search = ref('')

const headers = [
    { title: 'ID', key: 'id', align: 'start' },
    { title: 'Código', value: 'codigo' },
    { title: 'Descripción', value: 'descripcion' },
    { title: 'Precio', value: 'precio' },
    { title: 'Impuesto', value: 'impuesto' },
    { title: 'Acciones', key: 'actions', align: 'end', sortable: false },
]

const getProductos = async () => {
    loading.value = true
    try {
        const response = await axios.get('productos')
        productos.value = response.data
    } catch (error) {
        console.error(error)
    } finally {
        loading.value = false
    }
}

function add() {
    formModel.value = createNewRecord()
    dialog.value = true
}

function edit(id) {
    const found = productos.value.find(producto => producto.id === id)
    if (!found) return

    formModel.value = {
        id: found.id,
        codigo: found.codigo,
        descripcion: found.descripcion,
        precio: Number(found.precio),
        impuesto: Number(found.impuesto),
    }
    dialog.value = true
}

const save = async () => {
    try {
        if (isEditing.value) {
            await axios.put(`productos/${formModel.value.id}`, formModel.value)
        } else {
            await axios.post('productos', formModel.value)
        }
        await getProductos()
        dialog.value = false
    } catch (error) {
        console.error(error)
    }
}

const deleteProducto = async (id) => {
    if (!confirm('¿Eliminar este producto?')) return
    try {
        await axios.delete(`productos/${id}`)
        await getProductos()
    } catch (error) {
        console.error(error)
    }
}

function reset() {
    productos.value = []
}

onMounted(() => {
    getProductos()
})
</script>