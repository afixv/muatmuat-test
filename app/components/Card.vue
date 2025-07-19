<template>
  <div
    class="bg-white shadow-lg flex flex-col rounded-lg p-2 max-w-full group hover:shadow-xl transition-shadow">
    <BaseModal v-model="openModal">
      <div class="p-4 flex flex-col items-center">
        <h2 class="text-xl font-bold">Delete Product</h2>
        <p class=" text-gray-400 mt-1">
          Are you sure to delete the product?
        </p>
        <div class="flex gap-x-4 mt-4">
          <button class="btn btn-danger" @click="deleteProduct">
            Yes, delete
          </button>

          <button class="btn btn-secondary" @click="openModal = false">
            Cancel
          </button>
        </div>
      </div>
    </BaseModal>
    <div class="relative w-full h-48 overflow-hidden rounded-lg">
      <NuxtImg
        src="/img/default.jpg"
        alt="Default Image"
        class="rounded group-hover:scale-110 w-full h-full transition-all object-cover" />
      <NuxtLink
        :to="`/update/edit/${props.product?.slug}`"
        class="absolute top-2 right-2 cursor-pointer">
        <Icon
          v-if="route.path === '/update'"
          name="i-heroicons-pencil-solid"
          class="text-white bg-gray-700"
          size="20" />
      </NuxtLink>
    </div>
    <h2 class="text-lg font-semibold mt-2">{{ props.product?.title }}</h2>
    <p class="text-gray font-medium mt-2">Stok : {{ props.product?.stock }}</p>
    <p class="text-blue-500 font-bold mt-4">
      Rp{{ props.product?.price?.toLocaleString("id-ID") }}
    </p>
    <Icon
      v-if="route.path === '/update'"
      @click="openModal = true"
      name="i-heroicons-trash-solid"
      class="text-red-500 cursor-pointer mt-4" />
  </div>
</template>

<script lang="ts" setup>
import type { Product } from "~/types";

const openModal = ref(false);

const route = useRoute();
const props = defineProps({
  product: {
    type: Object as () => Product,
    required: true,
  },
});

const deleteProduct = () => {
  const products = localStorage.getItem("products");
  if (!products) return;
  const productArray = JSON.parse(products);
  const deleteProduct = productArray.filter(
    (p: Product) => p.slug !== props.product.slug
  );
  localStorage.setItem("products", JSON.stringify(deleteProduct));
  alert("Product deleted successfully!");
  location.reload();
};
</script>

<style></style>
