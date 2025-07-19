<template>
  <div class="container mx-auto p-4 max-w-7xl">
    <NuxtLink
      v-if="route.path === '/update'"
      to="/"
      class="text-blue-500 hover:underline mb-4"
      >Back to Home</NuxtLink
    >
    <div class="flex justify-between items-center mb-4">
      <h1 class="text-2xl font-bold">My Store</h1>
      <NuxtLink v-if="route.path === '/'" to="/update" class="btn btn-primary"
        >Update Products</NuxtLink
      >
      <NuxtLink v-else to="/update/add" class="btn btn-primary"
        >Add Products</NuxtLink
      >
    </div>
    <div class="flex flex-col md:flex-row gap-4 mb-6">
      <input
        v-model="search"
        type="text"
        placeholder="Search products..."
        class="w-full md:w-1/3" />
      <select v-model="sortBy" class="select select-bordered w-full md:w-1/4">
        <option value="">Sort By</option>
        <option value="price-asc">Price Ascending</option>
        <option value="price-desc">Price Descending</option>
        <option value="stock-asc">Stock Ascending</option>
        <option value="stock-desc">Stock Descending</option>
      </select>
    </div>
    <Loading v-if="loading" />
    <div
      v-if="filteredProducts.length > 0 && !loading"
      class="grid sm:grid-cols-2 md:grid-cols-4 gap-8 justify-center w-full">
      <Card v-for="p in filteredProducts" :key="p.id" :product="p" />
    </div>
    <div
      v-else-if="!filteredProducts && !loading"
      class="text-center text-gray-500 py-48">
      No products available. Please add some products.
    </div>
  </div>
</template>

<script lang="ts" setup>
import type { Product } from "~/types";

const search = ref("");
const sortBy = ref("");
const loading = ref(true);
const products = ref([] as Product[]);

const route = useRoute();

onMounted(() => {
  try {
    const storedProducts = localStorage.getItem("products");
    if (storedProducts) {
      products.value = JSON.parse(storedProducts);
    }
  } catch (error) {
    console.error("Failed to load products from localStorage:", error);
  } finally {
    loading.value = false;
  }
});

const debouncedSearch = ref(search.value);
let debounceTimeout: ReturnType<typeof setTimeout> | null = null;

watch(search, (newVal) => {
  if (debounceTimeout) clearTimeout(debounceTimeout);
  debounceTimeout = setTimeout(() => {
    debouncedSearch.value = newVal;
  }, 300);
});

const filteredProducts = computed(() => {
  let result = products.value;
  if (debouncedSearch.value.trim()) {
    const q = debouncedSearch.value.trim().toLowerCase();
    result = result.filter((p: Product) => p.title?.toLowerCase().includes(q));
  }
  if (sortBy.value === "price-asc") {
    result = [...result].sort((a, b) => a.price - b.price);
  } else if (sortBy.value === "price-desc") {
    result = [...result].sort((a, b) => b.price - a.price);
  } else if (sortBy.value === "stock-asc") {
    result = [...result].sort((a, b) => a.stock - b.stock);
  } else if (sortBy.value === "stock-desc") {
    result = [...result].sort((a, b) => b.stock - a.stock);
  }

  return result;
});
</script>
