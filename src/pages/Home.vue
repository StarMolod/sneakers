<script setup>
import {reactive, watch, ref, onMounted} from 'vue'
import axios from 'axios'
import {inject} from 'vue'
import CartList from '../components/CartList.vue'

const {cart, addToCart, removeFromCart} = inject('cart')

const items = ref([]);

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
   removeFromCart(item)
  }
  console.log(cart)
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
};

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value;
};

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }

      item.isFavorite = true;

      const { data } = await axios.post(`https://28b47f53a7f59a44.mokky.dev/favorites`, obj)
      
      item.favoriteId = data.id;
    } else {
      item.isFavorite = false;
      await axios.delete(`https://28b47f53a7f59a44.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
  } catch (err) {
    console.log(err)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(
      `https://28b47f53a7f59a44.mokky.dev/favorites`
    )
    // `https://604781a0efa572c1.mokky.dev/favorites`
    items.value = items.value.map(item => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id);

      if(!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      };
    });
  } catch (err) {
    console.log(err)
  }
}
  
const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
     
    };

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`;
    }

    const { data } = await axios.get(`https://28b47f53a7f59a44.mokky.dev/items`,

    {
      params
    }
    )
    items.value = data.map(obj => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false,
    }));
  } catch (err) {
    console.log(err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []
  
  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((CartItem) => CartItem.id === item.id)
  }))
});

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(filters, fetchItems)
</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-3 text-slate-600">Все кроссовки</h2>

    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене (дешевле)</option>
        <option value="-price">По цене (дороже)</option>
      </select>

      <div class="relative">
        <img class="absolute left-2 top-3" src="/search.svg" alt="" />
        <input
          @input="onChangeSearchInput"
          class="border rounded-md py-2 pl-8 pr-4 outline-none focus:border-gray-400"
          type="text"
          placeholder="Поиск..."
        />
      </div>
    </div>
  </div>

  <div class="mt-8">
    <CartList :items="items" @add-to-favorite="addToFavorite"
    @add-to-cart="onClickAddPlus"/>
  </div>
</template>