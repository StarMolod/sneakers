<script setup>
import { onMounted, ref, reactive, provide, watch, computed, withCtx} from 'vue';
import axios from 'axios'

import Header from './components/Header.vue'
import CartList from './components/CartList.vue'
import Drawer from './components/Drawer.vue'
import CartItem from './components/CartItem.vue';

const items = ref([]);
const cart = ref([]);
const isCreatingOrder = ref(false);


const drawerOpen = ref(false);

const totalPrice = computed(
  ()=> cart.value.reduce((acc,item) => acc + item.price, 0)
)
const vatPrice = computed(
  () => Math.round((totalPrice.value * 5)/100)
)
const cartIsEmpty = computed(()=>cart.value.length === 0);
const cartButtonDisabled = computed(()=>isCreatingOrder.value || cartIsEmpty.value);

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
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

const createOrder = async () => {
  try {
    isCreatingOrder.value = true;
    const {data} = await axios.post(`https://28b47f53a7f59a44.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: totalPrice.value
    })
    cart.value = []

    return data
  } catch (err) {
    console.log(err)
  } finally {
    isCreatingOrder.value = false
  }
}

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
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []
  
  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((CartItem) => CartItem.id === item.id)
  }))
});
watch(filters, fetchItems)

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(cart, () => {
  localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  {deep: true}
)

provide('cart', {
  cart,
  closeDrawer, 
  openDrawer,
  addToCart,
  removeFromCart,
})
</script>

<template>
  <Drawer v-if="drawerOpen" 
  :total-price="totalPrice" 
  :vat-price="vatPrice"
  @create-order="createOrder"
  :button-disabled="cartButtonDisabled"
  />

  <div class="bg-slate-50 w-5/6 m-auto rounded-xl shadow-xl mt-4">
    <Header :totalPrice="totalPrice" @open-drawer="openDrawer"/>

    <div class="p-8">
      <div class="flex justify-between items-center">
        <h2 class="text-4xl font-bold mb-3 text-slate-600">Все красовки</h2>

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
    </div>
  </div>
</template>
//6-13-39