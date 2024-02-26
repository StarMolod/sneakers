<script setup>
  import DrawerHead from './DrawerHead.vue';
  import CartListItem from './CartListItem.vue'
  import InfoBlock from './InfoBlock.vue';

  const emit = defineEmits(['createOrder'])

const props = defineProps({
    totalPrice: Number,
    vatPrice: Number,
    buttonDisabled: Boolean,
  })




</script>


<template>
  <div class="fixed top-o left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-1/3 h-full fixed right-0 top-0 z-20 p-6">
    <DrawerHead /> 

    <div v-if="!totalPrice" class="flex h-full items-center">
      <InfoBlock
      title="Корзина пустая" 
      description="Добавьте хотябы один товар, чтобы сделать заказ." 
      image-url="/package-icon.png"/>
    </div>

    <div v-else>
      <CartListItem/>
      <div class="flex flex-col gap-4 mt-7">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{totalPrice}} ₽</b>
        </div>
  
        <div class="flex gap-2">
          <span>Налог 5%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{vatPrice}} ₽</b>
        </div>
  
        <button
        :disabled="buttonDisabled"
        @click="() => emit('createOrder')"
        class="mt-4 bg-lime-500 w-full rounded-xl py-2 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 cursor-pointer">
          Оформить заказ
        </button>  
      </div>
    </div>
  </div>
</template>