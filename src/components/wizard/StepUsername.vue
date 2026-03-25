<template>
  <div class="step-username q-pa-lg">
    <div class="text-center q-mb-xl">
      <div class="flash-logo q-mb-md">⚡</div>
      <div class="text-h5 text-weight-bold q-mb-sm">
        Choose your Flash Address
      </div>
      <div class="text-body2 text-grey-5">
        Receive ecash payments at
        <span class="text-primary">you@ecash.flashapp.me</span>
      </div>
    </div>

    <q-input
      v-model="username"
      filled
      dark
      label="Username"
      placeholder="satoshi"
      :rules="[
        (v) => !!v || 'Username is required',
        (v) =>
          /^[a-z0-9_.-]+$/.test(v) || 'Only lowercase letters, numbers, _ . -',
        (v) => v.length >= 3 || 'At least 3 characters',
      ]"
      class="q-mb-md flash-input"
      @keyup.enter="next"
    >
      <template #append>
        <span class="text-grey-5 text-caption">@ecash.flashapp.me</span>
      </template>
    </q-input>

    <div v-if="checking" class="text-center q-mb-md">
      <q-spinner color="primary" size="1.5em" />
      <span class="q-ml-sm text-grey-5">Checking availability…</span>
    </div>

    <div v-if="available === true" class="text-positive q-mb-md">
      <q-icon name="check_circle" /> {{ username }}@ecash.flashapp.me is
      available!
    </div>
    <div v-if="available === false" class="text-negative q-mb-md">
      <q-icon name="cancel" /> That username is taken. Try another.
    </div>

    <q-btn
      class="full-width q-mt-md flash-btn"
      color="primary"
      :label="skipping ? 'Skip for now' : 'Continue'"
      :loading="checking"
      @click="next"
    />
    <q-btn
      flat
      class="full-width q-mt-sm text-grey-5"
      label="Skip for now"
      @click="$emit('skip')"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from "vue";
import { useFlashAddressStore } from "src/stores/flashAddress";

export default defineComponent({
  name: "StepUsername",
  emits: ["next", "skip"],
  setup(_, { emit }) {
    const username = ref("");
    const checking = ref(false);
    const available = ref<boolean | null>(null);
    const skipping = ref(false);
    const flashStore = useFlashAddressStore();

    let debounceTimer: ReturnType<typeof setTimeout> | null = null;

    watch(username, (val) => {
      available.value = null;
      if (debounceTimer) clearTimeout(debounceTimer);
      if (!val || val.length < 3) return;
      debounceTimer = setTimeout(() => checkAvailability(val), 600);
    });

    async function checkAvailability(name: string) {
      checking.value = true;
      try {
        const res = await fetch(
          `https://ecash.flashapp.me/api/register/${name.toLowerCase().trim()}`,
          { method: "HEAD" }
        );
        available.value = res.status === 404;
      } catch {
        available.value = null;
      } finally {
        checking.value = false;
      }
    }

    async function next() {
      if (!username.value || username.value.length < 3) {
        emit("skip");
        return;
      }
      flashStore.connect(username.value.toLowerCase().trim());
      emit("next");
    }

    return { username, checking, available, skipping, next };
  },
});
</script>

<style scoped>
.flash-logo {
  font-size: 3rem;
}
.flash-input :deep(.q-field__control) {
  border-radius: 12px;
  background: #1a1a1e;
  border: 1px solid rgba(255, 255, 255, 0.08);
}
.flash-btn {
  border-radius: 12px;
  font-weight: 600;
}
</style>
