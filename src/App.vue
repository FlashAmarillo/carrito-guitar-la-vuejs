<script setup>
  import { ref, reactive, onMounted, watch } from 'vue'
  import { db}  from './data/guitarras'
  import Guitarra from './components/Guitarra.vue'
  import Header from './components/Header.vue'
  import Footer from './components/Footer.vue'

  const guitarras = ref([])
  const carrito = ref([])
  const guitarra = ref({})

  watch(carrito, () => {
    guardarLocalStorage()
  }, {
    deep: true
  })

  // Método de ciclo de vida del componente, una vez se renderice se ejecuta este codigo
  onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]

    const carritoStorage = localStorage.getItem('carrito')
    if(carritoStorage) {
        carrito.value = JSON.parse(carritoStorage)
    }
  })

  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) => {
    // comprobamos si el elemento que agregamos ya existe en el carrito y obtenemos su posicion
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)

    if(existeCarrito >= 0) {
        // se actualiza la cantidad ya que ya se encuentra en el carrito
        carrito.value[existeCarrito].cantidad++
    } else {
        // se añade al carrito con cantidad = 1
        guitarra.cantidad = 1
        carrito.value.push(guitarra)
    }

    guardarLocalStorage()
  }

  const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad <= 1) return
    carrito.value[index].cantidad--
  }

  const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad >= 10) return
    carrito.value[index].cantidad++
  }

  const eliminarDelCarrito = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id)
  }

  const vaciarCarrito = () => {
    carrito.value = []
  }

</script>

<template>

    <Header 
        :carrito="carrito"
        :guitarra="guitarra"
        @incrementar-cantidad="incrementarCantidad"
        @disminuir-cantidad="decrementarCantidad"
        @eliminar-guitarra-del-carrito="eliminarDelCarrito"
        @agregar-carrito="agregarCarrito"
        @vaciar-carrito="vaciarCarrito"
    />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra 
                v-for="guitarra in guitarras"
                :key="guitarra.id" 
                v-bind:guitarra="guitarra"
                @agregar-carrito="agregarCarrito"
            />
        </div>
    </main>

    <Footer />

</template>

<style scoped>

</style>
