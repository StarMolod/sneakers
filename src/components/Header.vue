<script setup>
  defineProps({
    totalPrice: Number
  })


  const emit = defineEmits(['openDrawer']);
</script>

<template>
  <header class="flex justify-between border-b border-slate-200 px-8 py-8">
    <div class="flex items-center gap-4">
      <img src="/logo.png" alt="Logo" class="w-10">
      <div>
        <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
        <p class="text-slate-500">Магазин лучших кросовок</p>
      </div>
    </div>

    <ul class="flex items-center gap-6">
      <li @click="() => emit('openDrawer')" class="flex items-center cursor-pointer gap-2 text-gray-500 hover:text-black flex-wrap justify-center">
        <img src="/public/cart.svg" alt="Cart">
        <b>{{totalPrice}} руб.</b>
      </li>

      <li class="flex items-center cursor-pointer gap-2 text-gray-500 hover:text-black flex-wrap justify-center">
        <img src="/public/heart.svg" alt="Cart">
        <span>Закладки</span>
      </li>

      <li class="flex items-center cursor-pointer gap-2 text-gray-500 hover:text-black flex-wrap justify-center">
        <img src="/public/profile.svg" alt="Cart">
        <span>Профиль</span>
      </li>
    </ul>
  </header>
</template>