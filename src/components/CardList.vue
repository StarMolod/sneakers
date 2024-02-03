<script setup>
  import Card from './Card.vue'

defineProps({
  items: Array
})

  const onClickAdd = () => {
    alert('добавить1')
  }
</script>

<template>
  <div class="grid grid-cols-3 gap-5" >
    <Card 
    v-for="item in items"
      :key="item.id"
      :title="item.title" 
      :imageUrl="item.imageUrl"
      :price="item.price" 
      :isAdded="false"
      :isFavorite="false"
      :onClickAdd="onClickAdd" 
      />
    
  </div>
  
</template>