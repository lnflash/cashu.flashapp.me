<template>
  <div class="flash-shell">
    <slot />
    <SetupWizard v-model="showWizard" @complete="onWizardComplete" />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";
import SetupWizard from "./wizard/SetupWizard.vue";

const SETUP_DONE_KEY = "cashu.flash.setupDone";

function isFirstVisit(): boolean {
  return !localStorage.getItem(SETUP_DONE_KEY);
}

export default defineComponent({
  name: "FlashShell",
  components: { SetupWizard },
  setup() {
    const showWizard = ref(false);

    onMounted(() => {
      if (isFirstVisit()) {
        // Small delay so the main wallet UI renders first
        setTimeout(() => {
          showWizard.value = true;
        }, 800);
      }
    });

    function onWizardComplete() {
      showWizard.value = false;
    }

    return { showWizard, onWizardComplete };
  },
});
</script>

<style scoped>
.flash-shell {
  position: relative;
  width: 100%;
  height: 100%;
}
</style>
