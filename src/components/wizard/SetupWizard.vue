<template>
  <q-dialog
    v-model="show"
    persistent
    full-screen
    transition-show="slide-up"
    transition-hide="slide-down"
  >
    <q-card
      class="setup-wizard-card"
      style="background: #0a0a0a; color: #f5f5f5"
    >
      <!-- progress dots -->
      <div class="wizard-progress row justify-center q-pt-lg q-pb-sm">
        <div
          v-for="i in totalSteps"
          :key="i"
          class="progress-dot q-mx-xs"
          :class="{ active: i - 1 === currentStep, done: i - 1 < currentStep }"
        />
      </div>

      <q-carousel
        v-model="currentStep"
        animated
        :swipeable="false"
        style="height: calc(100% - 48px)"
      >
        <q-carousel-slide :name="0">
          <StepUsername @next="currentStep++" @skip="currentStep++" />
        </q-carousel-slide>
        <q-carousel-slide :name="1">
          <StepNostrKey @next="currentStep++" @skip="currentStep++" />
        </q-carousel-slide>
        <q-carousel-slide :name="2">
          <StepDone @done="complete" />
        </q-carousel-slide>
      </q-carousel>
    </q-card>
  </q-dialog>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";
import StepUsername from "./StepUsername.vue";
import StepNostrKey from "./StepNostrKey.vue";
import StepDone from "./StepDone.vue";

export default defineComponent({
  name: "SetupWizard",
  components: { StepUsername, StepNostrKey, StepDone },
  props: {
    modelValue: {
      type: Boolean,
      default: false,
    },
  },
  emits: ["update:modelValue", "complete"],
  setup(props, { emit }) {
    const currentStep = ref(0);
    const totalSteps = 3;

    const show = computed({
      get: () => props.modelValue,
      set: (v: boolean) => emit("update:modelValue", v),
    });

    function complete() {
      localStorage.setItem("cashu.flash.setupDone", "1");
      emit("complete");
      show.value = false;
    }

    return { show, currentStep, totalSteps, complete };
  },
});
</script>

<style scoped>
.setup-wizard-card {
  max-width: 480px;
  margin: 0 auto;
  border-radius: 0;
}
.wizard-progress {
  gap: 6px;
}
.progress-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.15);
  transition: background 0.3s;
}
.progress-dot.active {
  background: #41ad49;
}
.progress-dot.done {
  background: rgba(65, 173, 73, 0.5);
}
</style>
