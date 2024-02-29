<script setup>
  import {ref, onMounted} from 'vue'
  import axios from 'axios'
  import CartList from '../components/CartList.vue'

  const favorites = ref({})
  onMounted(async () => {
    try {
      const { data } = await axios.get(
        `https://28b47f53a7f59a44.mokky.dev/favorites?_relations=items`
        )
      favorites.value = data.map(obj => obj.item)
    } catch (err) {
      console.log(err)
    }
  })
</script>

<template>
  <h2 class="text-3xl font-bold mb-3 text-slate-600">Мои закладки</h2>
  <CartList :items="favorites" is-favorites/>
</template>