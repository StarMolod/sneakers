<script setup>
  import Cart from './Cart.vue'

defineProps({
  items: Array,
  isFavorites: Boolean
})

const emit = defineEmits(['addToFavorite', 'addToCart']);
</script>

<template>
  <div class="grid gap-5 grid-cols-3" v-auto-animate>
    <Cart 
    v-for="item in items"
      :key="item.id"
      :id="item.id"
      :title="item.title" 
      :imageUrl="item.imageUrl"
      :price="item.price" 
      :onClickFavorite="isFavorites ? null : () => emit('addToFavorite', item,)"
      :onClickAdd="isFavorites ? null : () => emit('addToCart', item,)" 
      :isFavorite="item.isFavorite"
      :isAdded="item.isAdded"
      />
  </div>
</template>