<template>
  <div class="log bg-gray-800 text-gray-300 font-mono flex flex-col">
    <div class="p-2 border-b border-gray-600 font-bold">Log</div>
    <div class="flex-grow overflow-y-auto p-2" ref="log">
      <pre v-for="(log,index) in logs" :key="`log-${index}`" class="mb-1">{{log}}</pre>
    </div>
  </div>
</template>


<script>
export default {
  props: {
    logs: {
      type: Array,
      default: () => [],
    },
  },

  watch: {
    logs() {
      this.$nextTick(() => {
        const logRef = this.$refs.log;
        logRef.scrollTop = logRef.scrollHeight;
      });
    },
  },
};
</script>


<style>
  .log {
    height: 24rem;
  }
</style>
