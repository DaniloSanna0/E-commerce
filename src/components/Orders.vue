<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <TreeTable :value="nodes">
    <Column field="id" header="Ordine n." sortable ></Column>
    <Column field="createdAt" header="Data" sortable></Column>
    <Column field="products" header="Prodotti" sortable></Column>
    <Column field="total" header="Totale" sortable></Column>
  </TreeTable>
</template>

<script setup>
import { ref, onMounted } from 'vue';
// import { orders } from '../../db.json';
import axios from '../api/axios';

const nodes = ref([]);

onMounted(async () => {
  try {
    const response = await axios.get('/orders'); 
    const orders = response.data || [];

    nodes.value = orders.map(order => ({
  data: {
    id: order.id,
    createdAt: new Date(order.createdAt).toLocaleDateString(),
    products: totalQuantity(order.products || []), 
    total: order.totalAmount, 
  }
}));
console.log(response.data)
  } catch (error) {
    console.error("Errore nel caricamento degli ordini:", error);
  }
});


const totalQuantity = (products) => {
  if (!Array.isArray(products)) return 0; 
  return products.reduce((total, product) => total + (product.quantity || 0), 0);
};

</script>
