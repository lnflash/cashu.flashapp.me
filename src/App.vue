<template>
  <router-view />
</template>

<script>
import { defineComponent } from "vue";
import { useMintsStore } from './stores/mints';

const mintsStore = useMintsStore();

onMounted(() => {
  mintsStore.init();
});

export default defineComponent({
  name: "App",
});
</script>
