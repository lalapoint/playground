<template>
<div class="h-screen flex flex-col justify-between">
  <div class="p-8 border-r">

    <div class="text-xl mb-2">API Playground</div>

    <div class="mb-4">
      <label class="font-bold">Token</label>
      <input type="text" class="form-input w-full" v-model="value.token">
    </div>

    <div class="mb-4">
      <select v-model="value.env" class="form-select w-full">
        <option disabled value="">Select Environment</option>
        <option v-for="opt in environments" :key="opt.name" :value="opt.url">{{opt.name}}</option>
      </select>
    </div>

    <label class="mb-2 flex items-center font-bold">
      <input type="checkbox" class="form-checkbox mr-2 h-5 w-5" v-model="value.hasStories">
      Has stories
    </label>

    <label class="mb-2 flex items-center font-bold">
      <input type="checkbox" class="form-checkbox mr-2 h-5 w-5" v-model="value.topStories">
      Top stories
    </label>
  </div>

  <div>

    <Log :logs="value.logs" />

  </div>
</div>
</template>


<script>

import Log from './Log.vue';

export default {
  props: {
    value: {
      type: Object,
      default: () => ({}),
    },
  },

  components: {
    Log,
  },

  data: () => ({
    environments: [
      process.env.NODE_ENV !== 'production' ? { name: 'Local', url: 'http://jasper.test' } : false,
      { name: 'Staging', url: 'https://jasper.lalapoint.com' },
      { name: 'Production', url: 'https://lalapoint.com' },
    ].filter(Boolean),
  }),

};
</script>
