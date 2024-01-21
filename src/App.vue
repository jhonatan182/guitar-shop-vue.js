<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'
import { GuitarraResponse, Carrito } from './interfaces';
import { db } from './data/guitarras'
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

// interface PropsState {
//     guitarras: Guitarra[]
// }

// const state: PropsState = reactive({
//     guitarras: db
// })

const guitarras = ref<GuitarraResponse[]>([])
const carrito = ref<Carrito[]>([])
const guitarra = ref<GuitarraResponse>({
    id: 0,
    nombre: '',
    imagen: '',
    descripcion: '',
    precio: 0
}
)


watch(carrito, () => {
    agregarLocalStorage()
}, {
    deep: true
})

onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]

    const carritoStorage = localStorage.getItem('carrito');

    if (carritoStorage) {
        carrito.value = JSON.parse(carritoStorage)
    }
})


const agregarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))

}

const agregarCarrito = (guitarra: GuitarraResponse) => {

    const existeCarrito: number = carrito.value.findIndex(producto => producto.id === guitarra.id)

    //ya existe en el carrito
    if (existeCarrito >= 0) {
        carrito.value[existeCarrito].cantidad++;

    } else {
        const guitarraCarrito: Carrito = {
            ...guitarra,
            cantidad: 1
        }
        carrito.value.push(guitarraCarrito)
    }

}

const decrementarCantidad = (id: number) => {

    const indexGuitarra: number = carrito.value.findIndex(producto => producto.id === id)

    if (carrito.value[indexGuitarra].cantidad <= 1) return

    carrito.value[indexGuitarra].cantidad--;

}

const incrementarCantidad = (id: number) => {
    const indexGuitarra: number = carrito.value.findIndex(producto => producto.id === id)

    if (carrito.value[indexGuitarra].cantidad >= 5) return

    carrito.value[indexGuitarra].cantidad++;

}

const eliminarProductoCarrito = (id: number) => {
    const productosFiltrados = carrito.value.filter(producto => producto.id !== id);
    carrito.value = productosFiltrados
}

const vaciarCarrito = () => {
    carrito.value = []
}

</script>

<template>
    <body>
        <Header v-bind:carrito="carrito" @decrementar-cantidad="decrementarCantidad"
            @incrementar-cantidad="incrementarCantidad" @agregar-carrito="agregarCarrito" :guitarra="guitarra"
            @eliminar-producto-carrito="eliminarProductoCarrito" @vaciar-carrito="vaciarCarrito" />

        <main class="container-xl mt-5">
            <h2 class="text-center">Nuestra Colección</h2>

            <div class="row mt-5">
                <Guitarra v-for="guitarra in guitarras" v-bind:guitarra="guitarra" @agregar-carrito="agregarCarrito" />
            </div>
        </main>

        <Footer />
    </body>
</template>

<style scoped></style>


