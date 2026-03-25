<template>
  <div class="step">
    <div class="step-center">
      <!-- Done checkmark -->
      <div class="done-circle">
        <svg width="40" height="40" viewBox="0 0 40 40" fill="none">
          <path
            d="M8 21L16 29L32 13"
            stroke="#41AD49"
            stroke-width="3.5"
            stroke-linecap="round"
            stroke-linejoin="round"
          />
        </svg>
      </div>

      <h1 class="step-title">You're set up.</h1>
      <p class="step-sub">
        Your Flash Address is ready. Share it to receive Bitcoin from anywhere.
      </p>

      <!-- Address card -->
      <div v-if="address" class="address-card" @click="copyAddress">
        <div class="address-card-label">YOUR FLASH ADDRESS</div>
        <div class="address-card-value">⚡ {{ address }}</div>
        <div class="address-card-hint">
          {{ copied ? "✓ Copied!" : "Tap to copy" }}
        </div>
      </div>
    </div>

    <div class="step-footer">
      <button class="btn btn-primary" @click="$emit('done')">
        Open my wallet →
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";

export default defineComponent({
  name: "StepDone",
  emits: ["done"],
  setup() {
    const copied = ref(false);

    const address = computed(() => {
      try {
        const u = JSON.parse(
          localStorage.getItem("cashu.flashAddress.username") || "null"
        );
        return u ? `${u}@ecash.flashapp.me` : null;
      } catch {
        return null;
      }
    });

    function copyAddress() {
      if (!address.value) return;
      navigator.clipboard.writeText(address.value).then(() => {
        copied.value = true;
        setTimeout(() => {
          copied.value = false;
        }, 2000);
      });
    }

    return { address, copied, copyAddress };
  },
});
</script>

<style scoped>
.step {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding: 0 24px;
  padding-top: max(48px, env(safe-area-inset-top, 48px));
  padding-bottom: max(40px, env(safe-area-inset-bottom, 40px));
  background: #0a0a0a;
}

.step-center {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  gap: 20px;
}

.done-circle {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: rgba(65, 173, 73, 0.12);
  border: 2px solid rgba(65, 173, 73, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
}

.step-title {
  font-size: 32px;
  font-weight: 800;
  letter-spacing: -0.02em;
  color: #f5f5f5;
  margin: 0;
}

.step-sub {
  font-size: 16px;
  color: #888;
  margin: 0;
  line-height: 1.6;
  max-width: 320px;
}

.address-card {
  width: 100%;
  background: rgba(65, 173, 73, 0.08);
  border: 1.5px solid rgba(65, 173, 73, 0.3);
  border-radius: 16px;
  padding: 20px;
  cursor: pointer;
  transition: border-color 0.15s;
}
.address-card:hover {
  border-color: rgba(65, 173, 73, 0.5);
}

.address-card-label {
  font-size: 11px;
  color: #666;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  margin-bottom: 8px;
}

.address-card-value {
  font-size: 18px;
  font-weight: 700;
  color: #41ad49;
  word-break: break-all;
  margin-bottom: 8px;
}

.address-card-hint {
  font-size: 12px;
  color: #555;
}

.step-footer {
  padding-top: 24px;
}

.btn {
  width: 100%;
  padding: 16px;
  border-radius: 14px;
  border: none;
  font-size: 16px;
  font-weight: 700;
  font-family: inherit;
  cursor: pointer;
  transition: opacity 0.15s, transform 0.1s;
}
.btn:active {
  transform: scale(0.98);
}
.btn-primary {
  background: #41ad49;
  color: #000;
}
.btn-primary:hover {
  background: #4ec256;
}
</style>
