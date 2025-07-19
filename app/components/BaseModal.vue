<template>
  <Teleport to="body">
    <Transition name="fade" mode="out-in">
      <div
        v-if="modelValue"
        class="fixed inset-0 bg-black/50 flex items-center justify-center z-50"
        @click.self="$emit('update:modelValue', false)">
        <div class="bg-white p-6 rounded-xl shadow-lg max-w-md w-full relative">
          <button
            class="absolute top-3 right-3 text-gray-400 hover:text-black text-xl"
            @click="$emit('update:modelValue', false)">
            <Icon name="material-symbols:close" />
          </button>
          <slot />
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
defineProps({
  modelValue: Boolean,
});

defineEmits(["update:modelValue"]);
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
.fade-enter-to,
.fade-leave-from {
  opacity: 1;
}
.fade-leave-active {
  transition: opacity 0.3s ease;
}
</style>
