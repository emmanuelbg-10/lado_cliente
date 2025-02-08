<script setup>

const props = defineProps({
  cartIds: Array,
  username: String
})

/**
 * Emits an event to remove an item from the shopping cart.
 * 
 * @event removeFromCart
 */
defineEmits(['removeFromCart', 'IncrementAmount', 'DecrementAmount', 'removeAllCart', 'buyCart']);

</script>

<template>
  <div class="shopping-cart">
    <h1>Carrito de la compra</h1>
    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Nombre</th>
          <th>Precio</th>
          <th>Cantidad</th>
          <th>Subtotal</th> 
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="cartId in cartIds" :key="cartId.id">
          <td>{{ cartId.id }}</td>
          <td>{{ cartId.name }}</td>
          <td>{{ cartId.price }}€</td>
          <td>
            <button @click="$emit('IncrementAmount', cartId.id)">+</button>
            <input type="number" v-model="cartId.amount">
            <button @click="$emit('DecrementAmount', cartId.id)">-</button>
          </td>
          <td>{{ (cartId.price * cartId.amount).toFixed(2) }}€</td>
          <td><button @click="$emit('removeFromCart', cartId.id)">Eliminar</button></td>
        </tr> 
      </tbody>
      <tfoot>
        <tr>
          <td colspan="4">Total</td>
          <td>{{ cartIds.reduce((acc, item) => acc + item.price * item.amount, 0).toFixed(2) }}€</td>
        </tr>
      </tfoot>
    </table>
    <p>
      <button v-if="username !== '' && cartIds.length !== 0" @click="$emit('buyCart')">Comprar Carrito</button>
      <button class="danger" @click="$emit('removeAllCart')">Elimina Todo</button>
    </p>
  </div>
</template>

<style scoped>
.shopping-cart {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.shopping-cart h1 {
  font-size: 2em;
  margin-bottom: 20px;
  text-align: center;
  color: #333;
}

.shopping-cart table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.shopping-cart th, .shopping-cart td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.shopping-cart th {
  background-color: #f4f4f4;
}

.shopping-cart tbody tr:hover {
  background-color: #f9f9f9;
}

.shopping-cart button {
  background: #007bff;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
}

.shopping-cart button:hover {
  background: #0056b3;
}

.shopping-cart button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.shopping-cart input[type="number"] {
  width: 50px;
  padding: 5px;
  text-align: center;
  border: 1px solid #ddd;
  border-radius: 5px;
  margin: 0 5px;
}

.shopping-cart p {
  text-align: center;
}

.shopping-cart p button {
  margin: 0 10px;
}

.shopping-cart .danger {
  background: #dc3545;
}
.shopping-cart .danger:hover {
  background: #c82333;
}
</style>