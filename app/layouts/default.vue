<template>
  <div class="relative min-h-screen">
    <slot />
    <div
      class="fixed bottom-12 left-1/2 transform -translate-x-1/2 bg-white shadow-lg rounded-xl py-4 px-12">
      <ul class="flex gap-12">
        <li v-for="item in routeData" :key="item.name">
          <NuxtLink
            :to="item.path"
            class="text-gray-600 hover:underline font-semibold"
            activeClass="!text-blue-500"
            >{{ item.name }}</NuxtLink
          >
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts" setup>
const routeData = [
  {
    name: "My Store",
    path: "/",
  },
  {
    name: "Fetch API",
    path: "/fetch",
  },
];

const route = useRoute();

function isActive(path: string) {
  return route.path === path;
}
</script>

<style></style>
