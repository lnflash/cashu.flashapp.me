<template>
  <div class="step-nostr q-pa-lg">
    <div class="text-center q-mb-xl">
      <div class="nostr-logo q-mb-md">🔑</div>
      <div class="text-h5 text-weight-bold q-mb-sm">Connect your Nostr key</div>
      <div class="text-body2 text-grey-5">
        Your Nostr key lets you sign in across apps and receive tips.
      </div>
    </div>

    <div v-if="nostrStore.pubkey" class="q-mb-lg">
      <div
        class="q-pa-md"
        style="
          background: rgba(65, 173, 73, 0.12);
          border: 1px solid rgba(65, 173, 73, 0.3);
          border-radius: 16px;
        "
      >
        <div class="row items-center">
          <q-icon
            name="verified"
            color="positive"
            size="1.5em"
            class="q-mr-sm"
          />
          <div>
            <div class="text-body2 text-weight-medium text-positive">
              Nostr key loaded
            </div>
            <div
              class="text-caption text-grey-5 text-truncate"
              style="max-width: 260px"
            >
              {{ shortPubkey }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <div v-else class="q-mb-lg">
      <q-btn
        class="full-width q-mb-sm flash-btn"
        color="primary"
        outline
        label="Generate new Nostr key"
        icon="add"
        @click="generateKey"
        :loading="generating"
      />
      <div class="text-center text-caption text-grey-6 q-mb-md">or</div>
      <q-input
        v-model="nsecInput"
        filled
        dark
        label="Import nsec key"
        placeholder="nsec1..."
        class="flash-input"
        :type="showNsec ? 'text' : 'password'"
      >
        <template #append>
          <q-btn
            flat
            dense
            round
            :icon="showNsec ? 'visibility_off' : 'visibility'"
            @click="showNsec = !showNsec"
          />
        </template>
      </q-input>
      <q-btn
        v-if="nsecInput"
        class="full-width q-mt-sm flash-btn"
        color="primary"
        label="Import key"
        @click="importKey"
      />
    </div>

    <q-btn
      class="full-width q-mt-md flash-btn"
      color="primary"
      label="Continue"
      @click="$emit('next')"
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
import { defineComponent, ref, computed } from "vue";
import { useNostrStore } from "src/stores/nostr";

export default defineComponent({
  name: "StepNostrKey",
  emits: ["next", "skip"],
  setup() {
    const nostrStore = useNostrStore();
    const nsecInput = ref("");
    const showNsec = ref(false);
    const generating = ref(false);

    const shortPubkey = computed(() => {
      if (!nostrStore.pubkey) return "";
      return nostrStore.pubkey.slice(0, 16) + "…" + nostrStore.pubkey.slice(-8);
    });

    async function generateKey() {
      generating.value = true;
      try {
        await nostrStore.generatePrivateKey();
      } finally {
        generating.value = false;
      }
    }

    async function importKey() {
      if (!nsecInput.value) return;
      try {
        await nostrStore.setPrivateKey(nsecInput.value);
        nsecInput.value = "";
      } catch (e) {
        console.error("[StepNostrKey] import failed:", e);
      }
    }

    return {
      nostrStore,
      nsecInput,
      showNsec,
      generating,
      shortPubkey,
      generateKey,
      importKey,
    };
  },
});
</script>

<style scoped>
.nostr-logo {
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
