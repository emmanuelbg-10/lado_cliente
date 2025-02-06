<script setup>
import { ref, onMounted, computed } from 'vue'
import ListProducts from './components/ListProducts.vue';
import ShoppingCart from './components/ShoppingCart.vue';

const username = ref('Emmanuel')
const products = ref([])
const cartIds = ref([])
const orderField = ref([])

onMounted(() => {
fetch("http://localhost:3000/api/products")
  .then(response => response.json())
  .then(data => products.value = data)
  .catch(error => console.log('error', error));

})

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
  if(cartId.amount > 1){
    cartId.amount--;
  }else{
    removeFromCart(id)
  }
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
  })
  .catch(error => console.log('error', error));
}
</script>

<template>
  <h1>Practiva Framework Vue 3</h1>
  <p><input type="text" v-model="username"></p>
  <p> Bienvenido {{ username }}</p>
  <p>Elementos del carrito: {{ cartIds.length }}</p>
  <p>
    Ordenar por:
    <input type="radio" v-model="orderField" name="radio" value="name" id="order-name">
    <label for="order-name">Nombre</label>
    <input type="radio" v-model="orderField" name="radio" value="price" id="order-list">
    <label for="order-price">Precio</label>
  </p>
  <ShoppingCart :cartIds="cartProducts" 
  @removeFromCart="removeFromCart"
  @incrementAmount="incrementAmount"
  @decrementAmount="decrementAmount"
  @removeAllCart="cartIds = []"
  />
  <ListProducts :products="productSort" @addToCart="addToCart" @deleteProduct="deleteProduct" />
</template>

<style scoped>
</style>