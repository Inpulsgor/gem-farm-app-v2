<template>
  <div class="nes-container with-title">
    <p class="title">Your Staking Account</p>
    <div class="mb-2">
      state:
      <p class="inline-block bg-yellow-200">
        {{ parseFarmerState(farmerAcc) }}
      </p>
    </div>
    <div class="mb-2">Your identity: {{ farmerAcc.identity.toBase58() }}</div>
    <div class="mb-2">Associated vault: {{ farmerAcc.vault.toBase58() }}</div>
    <div class="mb-2">Gems staked: {{ farmerAcc.gemsStaked }}</div>
    <div class="mb-2">
      Min staking ends: {{ parseDate(farmerAcc.minStakingEndsTs) }}
    </div>

    <div class="flex mb-5">
      <div class="flex-1 mr-5">
        <FarmerRewardDisplay
          :key="farmerAcc.rewardA"
          :farmReward="farmAcc.rewardA"
          :reward="farmerAcc.rewardA"
          title="Reward A"
        />
      </div>
      <div class="flex-1">
        <div class="hidden md:flex w-full md:w-1/2 mb-12 md:mb-0">
          <div class="faq flex flex-col p-6 w-full rounded-lg">
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
    <button class="nes-btn is-primary mb-5" @click="refreshFarmer">
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

<style scoped></style>
