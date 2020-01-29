<template>

<div class="mb-8 text-xl">
  <ul class="flex">
    <li
      class="block pr-1">
        Lalapoint
    </li>
    <li
      class="block pr-1"
      v-show="selected.state">
        &raquo; {{selected.state}}
    </li>
    <li
      class="block"
      v-show="selected.county">
        &raquo; {{selected.county}}
    </li>
    <li class="flex-1 text-right text-sm">
      <a
        class="text-blue-500"
        href="#"
        @click="$emit('reset')">
          Reset
      </a>
    </li>
  </ul>
</div>

</template>

<script>
export default {
  props: {
    selected: {
      type: Object,
      default: () => ({}),
    },
  },
};
</script>
