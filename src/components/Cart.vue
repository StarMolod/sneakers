<script setup>
const props = defineProps({
    id: Number,
    title: String,
    imageUrl: String,
    price: Number,
    isFavorite: Boolean,
    isAdded: Boolean,
    onClickFavorite: Function,
    onClickAdd: Function,
  })

  const visibleFavoriteButton = Boolean(props.onClickAdd)
</script>

<template>
  <div class="relative bg-white border border-slate-100 rounded-3xl p-4 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl">
    
    <img 
    v-if="onClickFavorite"
    :src="!isFavorite ? '/like-2.svg' : '/like-1.svg'" 
    alt="like 1" 
    class="absolute top-4 left-4"
    @click="onClickFavorite"
    >

    <img :src="imageUrl" alt="Sneaker">

    <p class="mt-2">{{ title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Цена:</span>
        <b>{{ price }} руб.</b>
      </div>

      <img v-if="onClickAdd"  @click="onClickAdd"
      class="w-9" :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="Plus">

    </div>
  </div>
</template>