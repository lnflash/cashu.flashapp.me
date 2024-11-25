<template>
  <router-view />
</template>

<script setup>
import { onMounted } from 'vue';
import { useMintsStore } from './stores/mints';

const mintStore = useMintsStore();

onMounted(async () => {
  await mintStore.init();
});
</script>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'App',
});
</script>