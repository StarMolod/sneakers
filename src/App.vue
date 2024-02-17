<script setup>
import { onMounted, ref, reactive, provide, watch} from 'vue';
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([]);
const cart = ref([])


const drawerOpen = ref(false);

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

const addToCart = (item) => {
  if (!item.isAdded) {
    cart.value.push(item)
    item.isAdded = true
  } else {
    cart.value.splice(cart.value.indexOf(item), 1)
    item.isAdded = false
  }
  console.log(cart)
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
};

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value;
};

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
  await fetchItems();
  await fetchFavorites();
});
watch(filters, fetchItems)

provide('cardActions', {
  closeDrawer, 
  openDrawer
})
</script>

<template>
  <Drawer v-if="drawerOpen" />

  <div class="bg-slate-50 w-5/6 m-auto rounded-xl shadow-xl mt-4">
    <Header @open-drawer="openDrawer"/>

    <div class="p-8">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все красовки</h2>

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
        <CardList :items="items" @add-to-favorite="addToFavorite"
        @add-to-cart="addToCart"/>
      </div>
    </div>
  </div>
</template>
//5-01-31
//fas