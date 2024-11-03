<template>
    <div class="relative">
        <svg width="20" height="20" fill="currentColor"
        class="absolute left-3 top-1/2 -mt-2.5 text-slate-400 pointer-events-none group-focus-within:text-blue-500"
        aria-hidden="true">
        <path fill-rule="evenodd" clip-rule="evenodd"
            d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z"
            class="" />
    </svg>
        <InputText v-model="serchedValue" placeholder="Search"
            class="focus:ring-2 focus:ring-blue-500 focus:outline-none appearance-none w-full text-sm leading-6 text-slate-900 placeholder-slate-400 rounded-md py-2 pl-10 ring-1 ring-slate-200 shadow-sm"
            type="text" aria-label="Filter projects" />
    </div>
    
    <div v-for="product in filteredProducts" :key="product.id">
        <div class="capitalize font-bold">{{ product.name }}</div>
        <div>Prezzo: {{ product.price }}</div>
        <button @click="addToCart(product)"
            class="h-10 px-6 font-semibold rounded-md bg-emerald-500 hover:bg-emerald-600 text-white">
            Aggiungi al carrello
        </button>
    </div>
</template>
<script setup>
// import { products } from '../../db.json'
import { useUserCartStore } from '../stores/userCart.store'
import { computed, ref } from 'vue'

const serchedValue = ref(null)
const store = useUserCartStore()

onMounted(async () => {
    try {
        const response = await axios.get('/api/products');
        products.value = response.data;
    } catch (error) {
        console.error("Errore nel caricamento dei prodotti:", error);
    }
});

const filteredProducts = computed(() => {
  const searchTerm = serchedValue.value ? serchedValue.value.toLowerCase() : ''

  return products.filter(product => product.name.toLowerCase().includes(searchTerm))
})

const addToCart = (product) => {
    store.addToCart(product)
}
</script>
<style lang="scss"></style>
