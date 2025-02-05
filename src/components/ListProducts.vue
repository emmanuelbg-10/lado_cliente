<script setup>
import { ref, computed } from 'vue'

const search = ref('')

const props = defineProps({
  products: Array,
})

const filteredProducts = computed(() => {
  return props.products.filter(product => {
    return product.name.toLowerCase().includes(search.value.toLowerCase())
  })})

  defineEmits(['addToCart']);
</script>

<template>
  <h1>Lista de Productos</h1>
  <p>Filtrar por 
    <input type="text" v-model="search" placeholder="Buscar por nombre">
  </p>
  <table>
    <thead>
      <tr>
        <th>Id</th>
        <th>Categoria</th>
        <th>Nombre</th>
        <th>Precio</th>
        <th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="product in filteredProducts" :key="product.id">
        <td>{{ product.id }}</td>
        <td>{{ product.category }}</td>
        <td>{{ product.name }}</td>
        <td>{{ product.price }}€</td>
        <td><button @click="$emit('addToCart', product.id)">Añadir al carrito</button></td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
h1 {
  color: blue;
}
table {
  width: 100%;
  border-collapse: collapse;
}
th, td {
  border: 1px solid black;
  padding: 5px;
}
th {
  background-color: lightblue;
}
tr:nth-child(even) {
  background-color: lightgray;
}

</style>