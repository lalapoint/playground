<template>
<div class="items">
    <slot v-for="item in items" :item="item" />
  </div>
</template>

<script>
export default {
  props: {
    items: {
      type: Array,
      default: () => [],
    },
  },
};
</script>

<style scoped>
 .items div {
  @apply p-4 border border-b-0 cursor-pointer flex justify-between items-center;
 }

.items div:hover {
  @apply bg-gray-200;
}

.items div:last-child {
  @apply border-b;
}
</style>
