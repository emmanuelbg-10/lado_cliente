<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import ListProducts from './components/ListProducts.vue';
import ShoppingCart from './components/ShoppingCart.vue';

const username = ref('')
const products = ref([])
const cartIds = ref([])
const orderField = ref([])

onMounted(() => {
  fetch("http://localhost:3000/api/products")
    .then(response => response.json())
    .then(data => {
      products.value = data;
      // Cargar carrito guardado después de cargar los productos
      const savedCart = localStorage.getItem('cart');
      if (savedCart) {
        cartIds.value = JSON.parse(savedCart);
      }
    })
    .catch(error => console.log('error', error));
});

watch(cartIds, (cartIds) => {
  localStorage.setItem('cart', JSON.stringify(cartIds));
}, { deep: true });

function addToCart(productId) {
  const cartId = cartIds.value.find(element => element.id === productId)
  if(cartId){
    cartId.amount++;
  }else{
    cartIds.value.push({id: productId, amount: 1})
  }
  console.log(cartIds.value)
}

function removeFromCart(id){
  cartIds.value = cartIds.value.filter(cartId => cartId.id !== id)
}

function incrementAmount(id){
  const cartId = cartIds.value.find(cartId => cartId.id === id)
  cartId.amount++;
}

function decrementAmount(id){
  const cartId = cartIds.value.find(cartId => cartId.id === id)
  cartId.amount--;
  if(cartId.amount <= 0){
    removeFromCart(id)
    return;
  }
}

function buyCart() {
  var raw = JSON.stringify({
    "user": username.value,
    "products": cartIds.value.map(cart => {
      return { "id": cart.id, "amount": cart.amount }
    })
  });

  var requestOptions = {
    method: 'POST',
    headers: { "Content-Type": "application/json" },
    body: raw
  };

  fetch("http://localhost:3000/api/orders", requestOptions)
    .then(response => response.json())
    .then(result => {
      console.log(result);
      alert('Compra realizada correctamente');
      cartIds.value = [];
    })
    .catch(error => console.log('error', error));
}
  
const cartProducts = computed(() => {
  return cartIds.value.map(item => {
    const product = products.value.filter(p => p.id === item.id)[0];
    item.name = product.name;
    item.price = product.price;
    return item;
  });
});

const productSort = computed(() => {
  if(orderField.value == 'name'){
    return products.value.sort((a,b) => a.name.localeCompare(b.name));
  }
  return products.value.sort((a,b) => a.price - b.price);
})

function deleteProduct(id){
  var requestOptions = {
  method: 'DELETE'
};

fetch(`http://localhost:3000/api/products/${id}`, requestOptions)
  .then(response => response.json())
  .then(result => {
    console.log(result);
    
    products.value = products.value.filter(product => product.id !== id);
    removeFromCart(id);
    alert('Producto eliminado correctamente');
  })
  .catch(error => console.log('error', error));
}
</script>

<template>
  <div class="container">
    <header>
      <h1>Practiva Framework Vue 3</h1>
      <h2 v-if="username !== ''"> Bienvenido {{ username }}</h2>
      <p><input type="text" v-model="username" placeholder="Introduce tu nombre..."></p>
    </header>
    <main>
      <section v-if="cartIds.length === 0" class="empty-cart">
        <p>No hay producto en el carrito</p>
      </section>
      <section v-else class="cart">
        <p>Elementos del carrito: {{ cartIds.length }}</p>
        <ShoppingCart :cartIds="cartProducts" :username="username"
          @removeFromCart="removeFromCart"
          @incrementAmount="incrementAmount"
          @decrementAmount="decrementAmount"
          @buyCart="buyCart"
          @removeAllCart="cartIds = []"
        />
      </section>
      <p>
          Ordenar por:
          <input type="radio" v-model="orderField" name="radio" value="name" id="order-name">
          <label for="order-name">Nombre</label>
          <input type="radio" v-model="orderField" name="radio" value="price" id="order-list">
          <label for="order-price">Precio</label>
        </p>
      <section class="products">
        <ListProducts :products="productSort" @addToCart="addToCart" @deleteProduct="deleteProduct" />
      </section>
    </main>
  </div>
</template>

<style scoped>
body{
  font-family: Arial, sans-serif;
  background-color: #333;
}
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

header {
  text-align: center;
  margin-bottom: 20px;
}

header h1 {
  font-size: 2.5em;
  margin-bottom: 10px;
}

header h2 {
  font-size: 1.5em;
  color: #333;
}

header input {
  padding: 10px;
  font-size: 1em;
  width: 100%;
  max-width: 300px;
  margin: 10px auto;
  display: block;
}

main {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.empty-cart {
  text-align: center;
  font-size: 1.2em;
  color: #999;
}

.cart, .products {
  background: #f9f9f9;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.cart p, .products p {
  margin: 10px 0;
}

.cart input[type="radio"] {
  margin-right: 5px;
}

.cart label {
  margin-right: 15px;
}

button {
  background: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background: #0056b3;
}

button:disabled {
  background: #ccc;
  cursor: not-allowed;
}
</style>