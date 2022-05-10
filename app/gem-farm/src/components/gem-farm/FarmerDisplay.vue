<template>
  <div class="acc-container with-title mt-8">
    <p class="text-2xl font-medium text-white mb-8">Your Staking Account</p>
    <ul class="flex flex-col mb-12">
      <li
        class="item flex flex-row justify-between md:justify-start md:gap-4 mb-5 pb-5"
      >
        <span class="item__label text-sm">State:</span>
        <span v-if="farmerAcc" class="item__value text-base text-white">
          {{ parseFarmerState(farmerAcc) }}
        </span>
        <span v-else class="item__value text-base text-white">{{ '--' }}</span>
      </li>
      <li class="item flex flex-col md:flex-row md:gap-4 mb-5 pb-5">
        <span class="item__label text-sm">Your identity:</span>
        <span v-if="farmerAcc" class="truncate text-white leading-4">{{
          farmerAcc.identity.toBase58()
        }}</span>
        <span v-else class="text-white leading-4">{{ '--' }}</span>
      </li>
      <li class="item flex flex-col md:flex-row md:gap-4 mb-5 pb-5">
        <span class="item__label text-sm">Associated vault:</span>
        <span v-if="farmerAcc" class="truncate text-white leading-4">{{
          farmerAcc.vault.toBase58()
        }}</span>
        <span v-else class="text-white leading-4">{{ '--' }}</span>
      </li>
      <li
        class="item flex flex-row justify-between md:justify-start md:gap-4 mb-5 pb-5"
      >
        <span class="item__label text-sm">DiamondHands staked</span>
        <span v-if="farmerAcc" class="text-base text-white">{{
          farmerAcc.gemsStaked
        }}</span>
        <span v-else class="text-base text-white">{{ '--' }}</span>
      </li>
      <li
        class="item flex flex-row justify-between md:justify-start md:gap-4 mb-5 pb-5"
      >
        <span class="item__label text-sm">Minimum staking ends</span>
        <span v-if="farmerAcc" class="text-base text-white">{{
          parseDate(farmerAcc.minStakingEndsTs)
        }}</span>
        <span v-else class="text-base text-white">{{ '--' }}</span>
      </li>
    </ul>

    <div class="flex flex-col md:flex-row mb-5 gap-x-4">
      <div class="flex-1">
        <FarmerRewardDisplay
          :key="farmerAcc.rewardA"
          :farmReward="farmAcc.rewardA"
          :reward="farmerAcc.rewardA"
          title="DMNDS"
        />
      </div>
      <div class="flex-1">
        <div class="hidden md:flex w-full">
          <div class="faq flex flex-col w-full rounded-lg">
            <span class="text-base text-white mb-5">How to stack?</span>

            <ul class="mb-4">
              <li class="text-sm mb-3">
                1. Make sure you are using the wallet which contains your DH NFT
              </li>
              <li class="text-sm mb-3">
                2. Open your browser and go to the staking link
              </li>
              <li class="text-sm mb-3">
                3. In the bar at bottom please select the wallet you are using
                and connect
              </li>
              <li class="text-sm mb-3 walletOverflow">
                4. In the address bar please input
                8EV1K3kWmq2hbRtQfnBg3wbvELEorJajbyJhRGwVptwj and click the new
                farmer button
              </li>
              <li class="text-sm mb-3">
                5. This will allow you to start staking
              </li>
              <li class="text-sm mb-3">
                6. Once the transaction goes through - the page will
                autopopulate
              </li>
              <li class="text-sm mb-3">
                7. Select you DH NFT by clicking on it and move it to the vault
                by clicking the arrow
              </li>
              <li class="text-sm mb-3">
                8. Click the green 'Move to Vault' button that has appeared and
                approve the transaction
              </li>
              <li class="text-sm mb-3">
                9. Once the NFT has moved to the vault select it and now click
                the begin staking button
              </li>
              <li class="text-sm mb-3">
                10. You will see your status update change to say 'staked'
              </li>
              <li class="text-sm mb-3">
                11. All done! Come back later and follow the process above - and
                then scroll down and click the Refresh account button to see how
                many tokens you've received!
              </li>
              <li class="text-sm mb-3">
                12. Once you've refreshed the account you'll see the claim token
                button update with the number of tokens you can claim
              </li>
              <li class="text-sm">
                13. Go ahead and claim those tokens! Make sure to come back to
                claim your rewards weekly
              </li>
            </ul>
            <iframe
              class="videoFrame"
              title="Inline Frame Example"
              allowFullScreen
              src="https://www.youtube.com/embed/7B9krM5zQCw"
            >
            </iframe>
          </div>
        </div>

        <div class="w-full md:hidden mb-6 accord rounded-lg">
          <input type="checkbox" name="panel" id="panel-1" class="hidden" />
          <label
            for="panel-1"
            class="accord__label relative block text-base text-white p-4 m-0"
            >How to stack?</label
          >

          <div class="accordion__content overflow-hidden">
            <div class="faqMobile flex flex-col p-6 w-full rounded-lg">
              <ul class="mb-4">
                <li class="text-sm mb-3">
                  1. Make sure you are using the wallet which contains your DH
                  NFT
                </li>
                <li class="text-sm mb-3">
                  2. Open your browser and go to the staking link
                </li>
                <li class="text-sm mb-3">
                  3. In the bar at bottom please select the wallet you are using
                  and connect
                </li>
                <li class="text-sm mb-3 walletOverflow">
                  4. In the address bar please input
                  8EV1K3kWmq2hbRtQfnBg3wbvELEorJajbyJhRGwVptwj and click the new
                  farmer button
                </li>
                <li class="text-sm mb-3">
                  5. This will allow you to start staking
                </li>
                <li class="text-sm mb-3">
                  6. Once the transaction goes through - the page will
                  autopopulate
                </li>
                <li class="text-sm mb-3">
                  7. Select you DH NFT by clicking on it and move it to the
                  vault by clicking the arrow
                </li>
                <li class="text-sm mb-3">
                  8. Click the green 'Move to Vault' button that has appeared
                  and approve the transaction
                </li>
                <li class="text-sm mb-3">
                  9. Once the NFT has moved to the vault select it and now click
                  the begin staking button
                </li>
                <li class="text-sm mb-3">
                  10. You will see your status update change to say 'staked'
                </li>
                <li class="text-sm mb-3">
                  11. All done! Come back later and follow the process above -
                  and then scroll down and click the Refresh account button to
                  see how many tokens you've received!
                </li>
                <li class="text-sm mb-3">
                  12. Once you've refreshed the account you'll see the claim
                  token button update with the number of tokens you can claim
                </li>
                <li class="text-sm">
                  13. Go ahead and claim those tokens! Make sure to come back to
                  claim your rewards weekly
                </li>
              </ul>
              <iframe
                class="videoFrame"
                title="Inline Frame Example"
                allowFullScreen
                src="https://www.youtube.com/embed/7B9krM5zQCw"
              >
              </iframe>
            </div>
          </div>
        </div>
      </div>
    </div>
    <button
      class="block w-full text-base text-white refresh rounded py-4 btnHeight md:max-w-xs md:m-auto"
      @click="refreshFarmer"
    >
      Refresh account
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, watch } from 'vue';
import FarmerRewardDisplay from '@/components/gem-farm/FarmerRewardDisplay.vue';
import useWallet from '@/composables/wallet';
import useCluster from '@/composables/cluster';
import { initGemFarm } from '@/common/gem-farm';
import { PublicKey } from '@solana/web3.js';
import { parseDate } from '@/common/util';

export default defineComponent({
  components: { FarmerRewardDisplay },
  props: {
    farm: String,
    farmAcc: Object,
    farmer: String,
    farmerAcc: Object,
  },
  emits: ['refresh-farmer'],
  setup(props, ctx) {
    const { wallet, getWallet } = useWallet();
    const { cluster, getConnection } = useCluster();

    let gf: any;
    watch([wallet, cluster], async () => {
      gf = await initGemFarm(getConnection(), getWallet()!);
    });

    //need an onmounted hook because this component isn't yet mounted when wallet/cluster are set
    onMounted(async () => {
      if (getWallet() && getConnection()) {
        gf = await initGemFarm(getConnection(), getWallet()!);
      }
    });

    // --------------------------------------- display farmer
    // todo ideally should be using one from client, but n/a during render
    const parseFarmerState = (farmer: any): string => {
      return Object.keys(farmer.state)[0];
    };

    const refreshFarmer = async () => {
      await gf.refreshFarmerWallet(
        new PublicKey(props.farm!),
        new PublicKey(props.farmer!)
      );
      ctx.emit('refresh-farmer');
    };

    return {
      refreshFarmer,
      parseFarmerState,
      parseDate,
    };
  },
});
</script>

<style scoped>
.acc-container {
  border: 1px solid #404040;
  border-radius: 4px;
  padding: 24px 32px;
}

.footer {
  position: static;
  width: 100%;
  background-color: transparent;
}
.footerSticky {
  position: sticky;
  bottom: 0;
  width: 100%;
  background-color: #000;
}
.bordered {
  border-top: 1px solid #404040;
}
.item {
  border-bottom: 1px solid #404040;
}
@media (min-width: 600px) {
  .item__label {
    min-width: 180px;
  }
  .reward__label {
    color: #d0d0d0;
    min-width: 160px;
  }
}
.item__label {
  color: #d0d0d0;
}
.item__value {
  color: #fff;
}
.truncate {
  max-width: 360px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.truncate:hover {
  white-space: unset;
  text-overflow: unset;
}
.refresh {
  border: 1px solid #ffffff;
}
.rewardTitle {
  color: #909090;
}
.farmerLabel {
  color: #999999;
}
.farmerInput {
  padding: 22px 16px;
  background: transparent;
  border: 1px solid #404040;
}
.farmerBtn {
  background: linear-gradient(90deg, #fbc7d4 0%, #9796f0 100%), #4aaf47;
  border-radius: 4px;
}
.staking {
  color: #141414;
  background: linear-gradient(192.54deg, #9be15d 2.01%, #00e3ae 97.9%),
    linear-gradient(90deg, #fbc7d4 0%, #9796f0 100%), #4aaf47;
}
.claim {
  color: #141414;
  background: linear-gradient(90deg, #fbc7d4 0%, #9796f0 100%), #4aaf47;
}
.reward {
  background: linear-gradient(
      90deg,
      rgba(251, 199, 212, 0.04) 0%,
      rgba(151, 150, 240, 0.04) 100%
    ),
    #191819;
}
.reward__fixed {
  color: #909090;
}
.reward__item {
  border-bottom: 1px solid #141414;
}
.btnHeight {
  max-height: 56px;
}
.faqMobile {
  height: max-content;
  color: #a1a1a1;
}
.faq {
  border: 1px solid #1d1d1d;
  height: max-content;
  color: #a1a1a1;
  padding: 16px;
}
.videoFrame {
  min-height: 186px;
}
.walletOverflow {
  overflow-wrap: anywhere;
}
@media (min-width: 768px) {
  .videoFrame {
    min-height: 286px;
  }
}
.accord__label:after {
  content: '+';
  position: absolute;
  right: 1em;
  color: #fff;
}
input:checked + .accord__label:after {
  content: '-';
  line-height: 0.8em;
}
.accordion__content {
  max-height: 0em;
  transition: all 0.4s cubic-bezier(0.865, 0.14, 0.095, 0.87);
}
input[name='panel']:checked ~ .accordion__content {
  /* Get this as close to what height you expect */
  max-height: min-content;
}
.accord {
  border: 1px solid #1d1d1d;
}
.btnHeight {
  /* max-height: 56px; */
}
</style>
