<template>
  <div class="step-done q-pa-lg text-center">
    <div class="done-animation q-mb-xl">
      <div class="done-circle">
        <q-icon name="bolt" color="positive" size="3rem" />
      </div>
    </div>

    <div class="text-h5 text-weight-bold q-mb-md">You're all set!</div>

    <div class="text-body1 text-grey-5 q-mb-xl">
      Your Flash Ecash wallet is ready to send and receive payments.
    </div>

    <div v-if="flashStore.enabled" class="address-card q-mb-xl q-pa-md">
      <div class="text-caption text-grey-6 q-mb-xs">Your Flash Address</div>
      <div class="text-body1 text-weight-medium text-primary">
        {{ flashStore.address }}
      </div>
    </div>

    <q-btn
      class="full-width flash-btn"
      color="primary"
      label="Start using Flash Ecash"
      size="lg"
      @click="$emit('done')"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { useFlashAddressStore } from "src/stores/flashAddress";

export default defineComponent({
  name: "StepDone",
  emits: ["done"],
  setup() {
    const flashStore = useFlashAddressStore();
    return { flashStore };
  },
});
</script>

<style scoped>
.done-circle {
  width: 96px;
  height: 96px;
  border-radius: 50%;
  background: rgba(65, 173, 73, 0.15);
  border: 2px solid rgba(65, 173, 73, 0.4);
  display: inline-flex;
  align-items: center;
  justify-content: center;
}
.address-card {
  background: rgba(65, 173, 73, 0.08);
  border: 1px solid rgba(65, 173, 73, 0.2);
  border-radius: 16px;
}
.flash-btn {
  border-radius: 12px;
  font-weight: 600;
}
</style>
