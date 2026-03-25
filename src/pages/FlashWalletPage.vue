<template>
  <div class="flash-wallet">
    <!-- Top bar -->
    <div class="flash-topbar">
      <span class="flash-wordmark">Flash</span>
      <button class="icon-btn" @click="showSettings = true">
        <svg width="22" height="22" viewBox="0 0 24 24" fill="none">
          <path
            d="M12 15a3 3 0 100-6 3 3 0 000 6z"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
          />
          <path
            d="M19.4 15a1.65 1.65 0 00.33 1.82l.06.06a2 2 0 010 2.83 2 2 0 01-2.83 0l-.06-.06a1.65 1.65 0 00-1.82-.33 1.65 1.65 0 00-1 1.51V21a2 2 0 01-4 0v-.09A1.65 1.65 0 009 19.4a1.65 1.65 0 00-1.82.33l-.06.06a2 2 0 01-2.83-2.83l.06-.06A1.65 1.65 0 004.68 15a1.65 1.65 0 00-1.51-1H3a2 2 0 010-4h.09A1.65 1.65 0 004.6 9a1.65 1.65 0 00-.33-1.82l-.06-.06a2 2 0 012.83-2.83l.06.06A1.65 1.65 0 009 4.68a1.65 1.65 0 001-1.51V3a2 2 0 014 0v.09a1.65 1.65 0 001 1.51 1.65 1.65 0 001.82-.33l.06-.06a2 2 0 012.83 2.83l-.06.06A1.65 1.65 0 0019.4 9a1.65 1.65 0 001.51 1H21a2 2 0 010 4h-.09a1.65 1.65 0 00-1.51 1z"
            stroke="currentColor"
            stroke-width="2"
          />
        </svg>
      </button>
    </div>

    <!-- Balance card -->
    <div class="balance-card">
      <div class="glow" />

      <!-- USD / BTC toggle -->
      <div class="unit-toggle">
        <button
          class="unit-btn"
          :class="{ active: unit === 'usd' }"
          @click="unit = 'usd'"
        >
          USD
        </button>
        <button
          class="unit-btn"
          :class="{ active: unit === 'btc' }"
          @click="unit = 'btc'"
        >
          BTC
        </button>
      </div>

      <!-- Balance -->
      <div class="balance-label">YOUR BALANCE</div>
      <div class="balance-amount">
        <span v-if="unit === 'usd'">{{ formatUsd(balanceSats) }}</span>
        <span v-else>{{ formatBtc(balanceSats) }}</span>
      </div>
      <div class="balance-sub">
        <span v-if="unit === 'usd'"
          >{{ balanceSats.toLocaleString() }} sats</span
        >
        <span v-else>{{ formatUsd(balanceSats) }}</span>
      </div>

      <!-- Lightning address chip -->
      <div v-if="flashAddress" class="address-chip" @click="copyAddress">
        <span class="chip-bolt">⚡</span>
        <span class="chip-username">{{ flashUsername }}</span>
        <span class="chip-domain">@ecash.flashapp.me</span>
        <button class="chip-copy" @click.stop="copyAddress">
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
            <rect
              x="9"
              y="9"
              width="13"
              height="13"
              rx="2"
              stroke="currentColor"
              stroke-width="2"
            />
            <path
              d="M5 15H4a2 2 0 01-2-2V4a2 2 0 012-2h9a2 2 0 012 2v1"
              stroke="currentColor"
              stroke-width="2"
            />
          </svg>
        </button>
      </div>
    </div>

    <!-- Action row -->
    <div class="action-row">
      <button
        class="action-btn action-btn--green"
        @click="$router.push('/receive')"
      >
        <div class="action-icon">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
            <path
              d="M12 5v14M5 12l7 7 7-7"
              stroke="currentColor"
              stroke-width="2.5"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </div>
        <span class="action-label">Receive</span>
      </button>
      <button class="action-btn" @click="showSend = true">
        <div class="action-icon">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
            <path
              d="M12 19V5M5 12l7-7 7 7"
              stroke="currentColor"
              stroke-width="2.5"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </div>
        <span class="action-label">Send</span>
      </button>
      <button class="action-btn" @click="showTopUp = true">
        <div class="action-icon">⚡</div>
        <span class="action-label">Top up</span>
      </button>
      <button class="action-btn" @click="showMore = true">
        <div class="action-icon">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
            <circle cx="5" cy="12" r="1.5" fill="currentColor" />
            <circle cx="12" cy="12" r="1.5" fill="currentColor" />
            <circle cx="19" cy="12" r="1.5" fill="currentColor" />
          </svg>
        </div>
        <span class="action-label">More</span>
      </button>
    </div>

    <!-- Transactions -->
    <div class="tx-section">
      <div class="tx-header">
        <span class="tx-title">Recent</span>
        <span class="tx-see-all" @click="showAllTx = !showAllTx">See all</span>
      </div>

      <div v-if="transactions.length === 0" class="tx-empty">
        No transactions yet. Share your address to receive sats.
      </div>

      <div v-for="tx in displayedTx" :key="tx.id" class="tx-row">
        <div
          class="tx-icon"
          :class="tx.amount > 0 ? 'tx-icon--in' : 'tx-icon--out'"
        >
          <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
            <path
              v-if="tx.amount > 0"
              d="M12 5v14M5 12l7 7 7-7"
              stroke="currentColor"
              stroke-width="2.5"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
            <path
              v-else
              d="M12 19V5M5 12l7-7 7 7"
              stroke="currentColor"
              stroke-width="2.5"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </div>
        <div class="tx-info">
          <div class="tx-name">
            {{ tx.label || (tx.amount > 0 ? "Received" : "Sent") }}
          </div>
          <div class="tx-sub">{{ txSubline(tx) }}</div>
        </div>
        <div
          class="tx-amount"
          :class="tx.amount > 0 ? 'tx-amount--in' : 'tx-amount--out'"
        >
          {{ tx.amount > 0 ? "+" : ""
          }}{{
            unit === "usd"
              ? formatUsd(Math.abs(tx.amount))
              : Math.abs(tx.amount).toLocaleString() + " sats"
          }}
        </div>
      </div>
    </div>

    <!-- Overlays (use existing cashu.me dialogs) -->
    <q-dialog v-model="showSend" position="bottom">
      <SendDialog />
    </q-dialog>

    <q-dialog v-model="showSettings" position="bottom">
      <div class="settings-sheet">
        <div class="sheet-handle" />
        <div class="sheet-title">Settings</div>
        <div class="settings-items">
          <div class="settings-item" @click="goToMints">
            <span>Manage mints</span>
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
              <path
                d="M9 18l6-6-6-6"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </div>
          <div
            class="settings-item"
            @click="
              showHistory = true;
              showSettings = false;
            "
          >
            <span>Full history</span>
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
              <path
                d="M9 18l6-6-6-6"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
      </div>
    </q-dialog>

    <!-- Toast -->
    <transition name="toast">
      <div v-if="toastMsg" class="toast">{{ toastMsg }}</div>
    </transition>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";
import { useProofsStore } from "src/stores/proofs";
import { useTokensStore } from "src/stores/tokens";
import { useFlashAddressStore } from "src/stores/flashAddress";
import { useMintsStore } from "src/stores/mints";

const FLASH_MINT = "https://forge.flashapp.me";
const SAT_TO_USD = 0.00096; // approximate, good enough for display

export default defineComponent({
  name: "FlashWalletPage",

  setup() {
    const router = useRouter();
    const proofsStore = useProofsStore();
    const tokensStore = useTokensStore();
    const flashStore = useFlashAddressStore();
    const mintsStore = useMintsStore();

    const unit = ref<"usd" | "btc">("usd");
    const showSend = ref(false);
    const showSettings = ref(false);
    const showTopUp = ref(false);
    const showMore = ref(false);
    const showHistory = ref(false);
    const showAllTx = ref(false);
    const toastMsg = ref("");
    const copiedAddr = ref(false);

    const balanceSats = computed(() => {
      const proofs = proofsStore.proofs || [];
      return proofs.reduce((s: number, p: any) => s + p.amount, 0);
    });

    const flashAddress = computed(() => flashStore.address);
    const flashUsername = computed(() => flashStore.username);

    const transactions = computed(() => {
      return (tokensStore.historyTokens || [])
        .filter((t: any) => t.amount !== 0)
        .sort(
          (a: any, b: any) =>
            new Date(b.date).getTime() - new Date(a.date).getTime()
        );
    });

    const displayedTx = computed(() =>
      showAllTx.value ? transactions.value : transactions.value.slice(0, 5)
    );

    function formatUsd(sats: number) {
      const usd = sats * SAT_TO_USD;
      return "$" + usd.toFixed(2);
    }

    function formatBtc(sats: number) {
      return (sats / 100_000_000).toFixed(8);
    }

    function txSubline(tx: any) {
      const parts = [];
      if (tx.mint) parts.push(tx.mint.replace("https://", "").split("/")[0]);
      if (tx.date) {
        const d = new Date(tx.date);
        const now = new Date();
        const diffH = (now.getTime() - d.getTime()) / 3600000;
        if (diffH < 24) parts.push(Math.round(diffH) + "h ago");
        else if (diffH < 48) parts.push("Yesterday");
        else
          parts.push(
            d.toLocaleDateString("en-US", { month: "short", day: "numeric" })
          );
      }
      return parts.join(" · ");
    }

    function showToast(msg: string) {
      toastMsg.value = msg;
      setTimeout(() => {
        toastMsg.value = "";
      }, 2000);
    }

    async function copyAddress() {
      if (!flashAddress.value) return;
      await navigator.clipboard.writeText(flashAddress.value);
      showToast("Address copied!");
    }

    function goToMints() {
      showSettings.value = false;
      router.push("/settings");
    }

    onMounted(async () => {
      // Ensure Flash Forge mint is added
      const mints = mintsStore.mints || [];
      const hasFlash = mints.some((m: any) => m.url === FLASH_MINT);
      if (!hasFlash) {
        try {
          await mintsStore.addMint({ url: FLASH_MINT });
        } catch {}
      }
      // Start flash address polling if configured
      if (flashStore.enabled) {
        flashStore.claimPending();
      }
    });

    return {
      unit,
      balanceSats,
      flashAddress,
      flashUsername,
      transactions,
      displayedTx,
      showSend,
      showSettings,
      showTopUp,
      showMore,
      showHistory,
      showAllTx,
      toastMsg,
      formatUsd,
      formatBtc,
      txSubline,
      copyAddress,
      goToMints,
    };
  },
});
</script>

<style scoped lang="scss">
* {
  box-sizing: border-box;
}

.flash-wallet {
  min-height: 100vh;
  background: #0a0a0a;
  color: #f5f5f5;
  font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Segoe UI",
    sans-serif;
  padding-bottom: 40px;
  max-width: 430px;
  margin: 0 auto;
}

/* Top bar */
.flash-topbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px 20px 12px;
}
.flash-wordmark {
  font-size: 17px;
  font-weight: 700;
  letter-spacing: -0.3px;
  color: #f5f5f5;
}
.icon-btn {
  background: none;
  border: none;
  color: #888;
  cursor: pointer;
  padding: 4px;
  display: flex;
  align-items: center;
  &:hover {
    color: #f5f5f5;
  }
}

/* Balance card */
.balance-card {
  position: relative;
  margin: 0 16px 20px;
  background: #141418;
  border: 1px solid rgba(255, 255, 255, 0.06);
  border-radius: 20px;
  padding: 20px;
  overflow: hidden;
}
.glow {
  position: absolute;
  top: -30px;
  right: -30px;
  width: 160px;
  height: 160px;
  background: radial-gradient(
    circle,
    rgba(65, 173, 73, 0.18) 0%,
    transparent 70%
  );
  pointer-events: none;
}

/* Unit toggle */
.unit-toggle {
  display: inline-flex;
  background: rgba(255, 255, 255, 0.06);
  border-radius: 999px;
  padding: 3px;
  margin-bottom: 16px;
}
.unit-btn {
  padding: 5px 16px;
  border-radius: 999px;
  border: none;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  color: #888;
  background: transparent;
  transition: all 0.15s;
  &.active {
    background: #41ad49;
    color: #000;
  }
}

.balance-label {
  font-size: 11px;
  letter-spacing: 0.08em;
  color: #666;
  font-weight: 500;
  margin-bottom: 6px;
}
.balance-amount {
  font-size: 42px;
  font-weight: 700;
  letter-spacing: -1.5px;
  line-height: 1;
  margin-bottom: 4px;
}
.balance-sub {
  font-size: 14px;
  color: #888;
  margin-bottom: 16px;
}

/* Address chip */
.address-chip {
  display: flex;
  align-items: center;
  gap: 6px;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 12px;
  padding: 10px 14px;
  cursor: pointer;
  &:hover {
    border-color: rgba(65, 173, 73, 0.3);
  }
}
.chip-bolt {
  font-size: 16px;
}
.chip-username {
  color: #41ad49;
  font-weight: 600;
  font-size: 14px;
}
.chip-domain {
  color: #888;
  font-size: 14px;
}
.chip-copy {
  margin-left: auto;
  background: none;
  border: none;
  color: #555;
  cursor: pointer;
  display: flex;
  align-items: center;
  &:hover {
    color: #f5f5f5;
  }
}

/* Action row */
.action-row {
  display: flex;
  gap: 12px;
  padding: 0 16px 24px;
}
.action-btn {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  background: #1a1a1e;
  border: 1px solid rgba(255, 255, 255, 0.06);
  border-radius: 16px;
  padding: 14px 8px;
  cursor: pointer;
  color: #f5f5f5;
  transition: all 0.15s;
  &:hover {
    border-color: rgba(255, 255, 255, 0.12);
  }
  &--green {
    background: #41ad49;
    border-color: #41ad49;
    color: #000;
    &:hover {
      background: #4dc656;
    }
  }
}
.action-icon {
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.08);
  .action-btn--green & {
    background: rgba(0, 0, 0, 0.15);
  }
}
.action-label {
  font-size: 12px;
  font-weight: 600;
}

/* Transactions */
.tx-section {
  padding: 0 16px;
}
.tx-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}
.tx-title {
  font-size: 17px;
  font-weight: 700;
}
.tx-see-all {
  font-size: 14px;
  color: #41ad49;
  cursor: pointer;
}
.tx-empty {
  font-size: 14px;
  color: #555;
  text-align: center;
  padding: 32px 0;
}
.tx-row {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 14px 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  &:last-child {
    border-bottom: none;
  }
}
.tx-icon {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  &--in {
    background: rgba(65, 173, 73, 0.15);
    color: #41ad49;
  }
  &--out {
    background: rgba(255, 255, 255, 0.06);
    color: #888;
  }
}
.tx-info {
  flex: 1;
  min-width: 0;
}
.tx-name {
  font-size: 15px;
  font-weight: 600;
}
.tx-sub {
  font-size: 12px;
  color: #666;
  margin-top: 2px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.tx-amount {
  font-size: 15px;
  font-weight: 700;
  &--in {
    color: #41ad49;
  }
  &--out {
    color: #f5f5f5;
  }
}

/* Settings sheet */
.settings-sheet {
  background: #141418;
  border-radius: 20px 20px 0 0;
  padding: 12px 20px 40px;
}
.sheet-handle {
  width: 36px;
  height: 4px;
  background: rgba(255, 255, 255, 0.15);
  border-radius: 99px;
  margin: 0 auto 20px;
}
.sheet-title {
  font-size: 17px;
  font-weight: 700;
  margin-bottom: 16px;
}
.settings-items {
  display: flex;
  flex-direction: column;
  gap: 2px;
}
.settings-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 14px 0;
  font-size: 15px;
  cursor: pointer;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  color: #f5f5f5;
  &:hover {
    color: #41ad49;
  }
}

/* Toast */
.toast {
  position: fixed;
  bottom: 32px;
  left: 50%;
  transform: translateX(-50%);
  background: #41ad49;
  color: #000;
  font-weight: 600;
  font-size: 14px;
  padding: 10px 20px;
  border-radius: 99px;
  z-index: 9999;
}
.toast-enter-active,
.toast-leave-active {
  transition: all 0.2s;
}
.toast-enter-from,
.toast-leave-to {
  opacity: 0;
  transform: translateX(-50%) translateY(8px);
}
</style>
