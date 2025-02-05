<script setup>

import { ref, onMounted } from 'vue'

const props = defineProps({
  cartIds: Array
})

/**
 * Emits an event to remove an item from the shopping cart.
 * 
 * @event removeFromCart
 */
defineEmits(['removeFromCart']);


</script>

<template>
  <h1>Carrito de la compra</h1>
  <table>
    <thead>
        <td>Id</td>
        <td>Cantidad</td> 
        <th>Acciones</th>
    </thead>
    <tbody>
      <tr v-for="cartId in cartIds" :key="cartId.id">
        <td>{{ cartId.id }}</td>
        <td>{{ cartId.amount }}</td>
        <td><button @click="$emit('removeFromCart', cartId.id)">Eliminar</button></td>
      </tr> 
    </tbody>
  </table>
</template>

<style scoped>

</style>