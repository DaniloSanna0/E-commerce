<template>
  <!-- <div>
    <div v-for="(order, index) in orders" :key="index" class="border">
      <div>Ordine n. {{ order.id }}</div>
      <div>Eseguito il: {{ order.createdAt }}</div>
      <div>Prodotti acquistati: {{ totalQuantity(order.products) }}</div>
      <div>Totale ordine: {{ order.total }}</div>
    </div>
  </div> -->
  <TreeTable :value="nodes">
    <Column field="id" header="Ordine n." sortable ></Column>
    <Column field="createdAt" header="Data" sortable></Column>
    <Column field="products" header="Prodotti" sortable></Column>
    <Column field="total" header="Totale" sortable></Column>
  </TreeTable>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { orders } from '../../db.json';

const nodes = ref([]);

onMounted(() => {
  nodes.value = orders.map(order => ({
    data: {
      id: order.id,
      createdAt: new Date(order.createdAt).toLocaleDateString(),
      products: totalQuantity(order.products),
      total: order.total,
    }
  }));
});

const totalQuantity = (products) => {
  return products.reduce((total, product) => total + (product.quantity || 0), 0);
};
</script>
