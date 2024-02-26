<script setup>
  defineProps({
    title: String,
    description: String,
    imageUrl: String
  })
</script>


<template>
  <div class="flex flex-col items-center text-center mx-auto">
    <img height="100" width="100" class="" :src="imageUrl" alt="Info Img">
    <h2 class="mt-2 pl-4 text-2xl font-mediun">{{title}}</h2>
    <p class="text-gray-400 mt-2">{{description}}</p>
  </div>
</template>