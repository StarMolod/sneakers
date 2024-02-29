<script setup>
import {ref, provide, watch, computed,} from 'vue';
import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

/* корзина START*/
const cart = ref([]);
const drawerOpen = ref(false);

const totalPrice = computed(
  ()=> cart.value.reduce((acc,item) => acc + item.price, 0)
)

const vatPrice = computed(
  () => Math.round((totalPrice.value * 5)/100)
)

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}



watch(cart, () => {
  localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  {deep: true}
)

provide('cart', {
  cart,
  closeDrawer, 
  openDrawer,
  addToCart,
  removeFromCart,
})
/* корзина END*/
</script>

<template>
  <Drawer v-if="drawerOpen" 
  :total-price="totalPrice" 
  :vat-price="vatPrice"
  />

  <div class="bg-slate-50 w-5/6 m-auto rounded-xl shadow-xl mt-4 mb-8 mt-8">
    <Header :totalPrice="totalPrice" @open-drawer="openDrawer"/>

    <div class="p-8" >
      <router-view>

      </router-view>
    </div>
  </div>
</template>
//dsa