<script setup>

const props = defineProps({
  cartIds: Array
})

/**
 * Emits an event to remove an item from the shopping cart.
 * 
 * @event removeFromCart
 */
defineEmits(['removeFromCart', 'IncrementAmount', 'DecrementAmount', 'removeAllCart']);


</script>

<template>
  <h1>Carrito de la compra</h1>
  <table>
    <thead>
        <th>Id</th>
        <th>Nombre</th>
        <th>Precio</th>
        <th>Cantidad</th>
        <th>Subtotal</th> 
        <th>Acciones</th>
    </thead>
    <tbody>
      <tr v-for="cartId in cartIds" :key="cartId.id">
        <td>{{ cartId.id }}</td>
        <td>{{ cartId.name }}</td>
        <td>{{ cartId.price }}</td>
        <td> <button @click="$emit('IncrementAmount', cartId.id)">+ </button> <input type="number" v-model="cartId.amount"> <button @click="$emit('DecrementAmount', cartId.id)"> - </button></td>
        <td>{{ (cartId.price * cartId.amount).toFixed(2) }}</td>
        <td><button @click="$emit('removeFromCart', cartId.id)">Eliminar</button></td>
      </tr> 
    </tbody>
  </table>
  <p>
    <button @click="$emit('removeAllCart')">Elimina Todo</button>
  </p>
</template>

<style scoped>

</style>