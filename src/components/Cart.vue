<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div>
      <h2 class="mb-2 font-extrabold font-sans">Carrello</h2>
      <div v-if="cart.length === 0" class="font-mono">
          Il carrello è vuoto 😱.
      </div>
      <div v-else>
          <div v-for="product in cart" :key="product.id">
              <div class="capitalize font-bold">{{ product.name }}</div>
              <div>Prezzo: {{ product.price }}</div>
              <button @click="removeFromCart(product)" class="h-10 px-6 font-semibold rounded-md bg-amber-600 hover:bg-amber-700 text-white">
                  Rimuovi dal carrello
              </button>
          </div>
          <div class="text-sm mt-4 font-semibold text-slate-700">
              Totale: €{{ calculateTotal() }}
          </div>
          <button @click="removeAll" class="h-10 mb-4 px-6 font-semibold rounded-md bg-rose-700 hover:bg-rose-600 text-white">
              Rimuovi tutto
          </button>
          <br>
          <button @click="buy" class="h-10 px-6 font-semibold rounded-md bg-teal-600 hover:bg-teal-700 text-white">
              Compra
          </button>
      </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useUserCartStore } from '../stores/userCart.store';
import { Order } from "@/api/index.js";

const userCartStore = useUserCartStore();
const cart = ref([]);

onMounted(() => {
  cart.value = userCartStore.cart;
});

const removeFromCart = (product) => {
  userCartStore.removeFromCart(product);
};

const removeAll = () => {
  userCartStore.clearCart();
  cart.value = [];
};

const formatCart = () => {
  return userCartStore.cart.reduce((accumulatore, product) => {
      accumulatore.totalPrice += Number(product.price) * (product.quantity || 1);

      const productInCart = accumulatore.products.find(p => p.productId === product.id);
      if (productInCart) {
          productInCart.quantity++;
      } else {
          accumulatore.products.push({
              productId: product.id,
              price: product.price,
              quantity: product.quantity || 1
          });
      }
      return accumulatore;
  }, { products: [], totalPrice: 0 });
};

const buy = async () => {
  console.log('Inizio invio ordine...');

  const formattedCart = formatCart();
  const order = {
      createdAt: new Date().toISOString(),
      status: "In Attesa",
      total: formattedCart.totalPrice,
      products: formattedCart.products
  };

  try {
      const response = await Order.sendOrder(order);
      console.log("Ordine inviato con successo:", response.data);
      removeAll(); 
  } catch (error) {
      console.error("Errore durante l'invio dell'ordine:", error);
  } finally {
      console.log('Fine invio ordine.');
  }
};

const calculateTotal = () => {
  return cart.value.reduce((total, product) => total + Number(product.price) * (product.quantity || 1), 0);
};
</script>

<style lang="scss">
</style>
