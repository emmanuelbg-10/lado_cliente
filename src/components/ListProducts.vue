<script setup>
import { ref, computed, onMounted } from "vue";

const search = ref("");
const searchCategory = ref("");
const categories = ref("");

const props = defineProps({
  products: Array,
});

const filteredProducts = computed(() => {
  return props.products.filter((product) => {
    return product.name.toLowerCase().includes(search.value.toLowerCase())
    && (searchCategory.value == "" ||  product.category == searchCategory.value)
  });
});

defineEmits(["addToCart", 'deleteProduct']);

onMounted(() => {
  fetch("http://localhost:3000/api/categories")
    .then((response) => response.json())
    .then((data) => categories.value = data)
    .catch((error) => console.log("error", error));
});
</script>

<template>
  <div class="product-list">
    <h1>Lista de Productos</h1>
    <div class="filters">
      <p>
        Filtrar por
        <input type="text" v-model="search" placeholder="Buscar por nombre" />
      </p>
      <p>
        Seleccionar categoría:
        <select v-model="searchCategory">
          <option value="">Cualquier categoría</option>
          <option v-for="(category, index) in categories" :key="index">{{ category }}</option>
        </select>
      </p>
    </div>
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
        <tr v-if="filteredProducts.length === 0">
          <td colspan="5" style="text-align: center;">No hay productos que coincidan con la búsqueda</td>
        </tr>
        <tr v-else v-for="product in filteredProducts" :key="product.id" :class="{ warning: product.category == 'Alcoholic' }">
          <td>{{ product.id }}</td>
          <td>{{ product.category }}</td>
          <td>{{ product.name }}</td>
          <td>{{ product.price }}€</td>
          <td>
            <button @click="$emit('addToCart', product.id)">
              Añadir al carrito
            </button>
            <button @click="$emit('deleteProduct', product.id)">Eliminar Producto</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.product-list {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.product-list h1 {
  font-size: 2em;
  margin-bottom: 20px;
  text-align: center;
  color: #333;
}

.filters {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

.filters p {
  margin: 0;
}

.filters input,
.filters select {
  padding: 10px;
  font-size: 1em;
  border: 1px solid #ddd;
  border-radius: 5px;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

th,
td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #f4f4f4;
}

button {
  background: #007bff;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
  margin: 10px;
}

button:hover {
  background: #0056b3;
}

button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

tr.warning {
  background-color: #dc3545;
  color: white;
}
</style>