<script setup>
  const emit = defineEmits('onClickRemove')

  defineProps({
    id: Number,
    title: String,
    imageUrl: String,
    price: Number,
  })
</script>

<template>
  <div class="flex items-center border border-slate-200 p-2 rounded-xl gap-4 mt-2">
    <img class="w-1/3 h-1/3" :src="imageUrl" :alt="title">

    <div class="flex self-end flex-col ml-2 text-sm flex-1">
      <p>{{title}}</p>


      <div class="flex justify-between  mt-4">
        <b class="flex-1">{{price}} руб.</b>
        <img @click="emit('onClickRemove')"
        class="opacity-50 hover:opacity-100 cursor-pointer transition" src="/close.svg" alt="close">
      </div>
    </div>
  </div>
</template>