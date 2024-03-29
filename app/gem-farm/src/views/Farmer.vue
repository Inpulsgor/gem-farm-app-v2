<template>
  <ConfigPane />
  <div v-if="!wallet" class="text-center text-gray-500">
    Pls connect your wallet
  </div>
  <div v-else>
    <!--farm address-->
    <div class="mx-auto flex flex-col md:flex-row md:items-end wrap">
      <div class="w-full mb-2 md:mb-0 md:mr-2">
        <label class="mb-2 farmerLabel" for="farm">Farm address</label>
        <input
          id="farm"
          class="w-full farmerInput rounded text-xs text-white"
          v-model="farm"
        />
      </div>

      <button
        class="text-base font-medium w-full text-black farmerBtn"
        @click="initFarmer"
      >
        New Farmer
      </button>
    </div>

    <div v-if="farmerAcc">
      <FarmerDisplay
        :key="farmerAcc"
        :farm="farm"
        :farmAcc="farmAcc"
        :farmer="farmer"
        :farmerAcc="farmerAcc"
        class="mb-10"
        @refresh-farmer="handleRefreshFarmer"
      />
      <Vault
        :key="farmerAcc"
        class="mb-10"
        :vault="farmerAcc.vault.toBase58()"
        @selected-wallet-nft="handleNewSelectedNFT"
      >
        <button
          v-if="farmerState === 'staked' && selectedNFTs.length > 0"
          class="is-primary w-6/12 rounded max-w-xs py-4 text-base font-medium btnHeight"
          @click="addGems"
        >
          Add Gems (resets staking)
        </button>
        <button
          v-if="farmerState === 'unstaked'"
          class="is-success w-6/12 rounded max-w-xs py-4 text-base font-medium staking btnHeight"
          @click="beginStaking"
        >
          Begin staking
        </button>
        <button
          v-if="farmerState === 'staked'"
          class="is-error w-6/12 rounded max-w-xs py-4 text-base font-medium staking btnHeight"
          @click="endStaking"
        >
          End staking
        </button>
        <button
          v-if="farmerState === 'pendingCooldown'"
          class="is-error w-6/12 rounded max-w-xs py-4 text-base font-medium staking btnHeight"
          @click="endStaking"
        >
          End cooldown
        </button>
        <button
          class="is-warning w-6/12 rounded max-w-xs py-4 text-base font-medium claim btnHeight"
          @click="claim"
        >
          Claim {{ availableA }} A
        </button>
      </Vault>
    </div>
    <div v-else>
      <div class="w-full text-center pt-4">
        Account not found - Please input
        TXXfHyyABWbjrbyZkageC5B6sz77m9DEhWf2DrDX1w7
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, nextTick, onMounted, ref, watch } from 'vue';
import useWallet from '@/composables/wallet';
import useCluster from '@/composables/cluster';
import { initGemFarm } from '@/common/gem-farm';
import { PublicKey } from '@solana/web3.js';
import ConfigPane from '@/components/ConfigPane.vue';
import FarmerDisplay from '@/components/gem-farm/FarmerDisplay.vue';
import Vault from '@/components/gem-bank/Vault.vue';
import { INFT } from '@/common/web3/NFTget';
import { findFarmerPDA, stringifyPKsAndBNs } from '@gemworks/gem-farm-ts';

export default defineComponent({
  components: { Vault, FarmerDisplay, ConfigPane },
  setup() {
    const { wallet, getWallet } = useWallet();
    const { cluster, getConnection } = useCluster();

    let gf: any;
    watch([wallet, cluster], async () => {
      if (cluster.value === 'mainnet') {
        farm.value = 'TXXfHyyABWbjrbyZkageC5B6sz77m9DEhWf2DrDX1w7';
      }

      // TXXfHyyABWbjrbyZkageC5B6sz77m9DEhWf2DrDX1w7
      await freshStart();
    });

    //needed in case we switch in from another window
    onMounted(async () => {
      await freshStart();
    });

    // --------------------------------------- farmer details
    const farm = ref<string>();
    const farmAcc = ref<any>();

    const farmerIdentity = ref<string>();
    const farmerAcc = ref<any>();
    const farmerState = ref<string>();

    const availableA = ref<string>();
    const availableB = ref<string>();

    //auto loading for when farm changes
    watch(farm, async () => {
      await freshStart();
    });

    const updateAvailableRewards = async () => {
      availableA.value = farmerAcc.value.rewardA.accruedReward
        .sub(farmerAcc.value.rewardA.paidOutReward)
        .toString();
      availableB.value = farmerAcc.value.rewardB.accruedReward
        .sub(farmerAcc.value.rewardB.paidOutReward)
        .toString();
    };

    const fetchFarn = async () => {
      farmAcc.value = await gf.fetchFarmAcc(new PublicKey(farm.value!));
      console.log(
        `farm found at ${farm.value}:`,
        stringifyPKsAndBNs(farmAcc.value)
      );
    };

    const fetchFarmer = async () => {
      const [farmerPDA] = await findFarmerPDA(
        new PublicKey(farm.value!),
        getWallet()!.publicKey!
      );
      farmerIdentity.value = getWallet()!.publicKey?.toBase58();
      farmerAcc.value = await gf.fetchFarmerAcc(farmerPDA);
      farmerState.value = gf.parseFarmerState(farmerAcc.value);
      await updateAvailableRewards();
      console.log(
        `farmer found at ${farmerIdentity.value}:`,
        stringifyPKsAndBNs(farmerAcc.value)
      );
    };

    const freshStart = async () => {
      if (getWallet() && getConnection()) {
        gf = await initGemFarm(getConnection(), getWallet()!);
        farmerIdentity.value = getWallet()!.publicKey?.toBase58();

        //reset stuff
        farmAcc.value = undefined;
        farmerAcc.value = undefined;
        farmerState.value = undefined;
        availableA.value = undefined;
        availableB.value = undefined;

        try {
          await fetchFarn();
          await fetchFarmer();
        } catch (e) {
          console.log(`farm with PK ${farm.value} not found :(`);
        }
      }
    };

    const initFarmer = async () => {
      await gf.initFarmerWallet(new PublicKey(farm.value!));
      await fetchFarmer();
    };

    // --------------------------------------- staking
    const beginStaking = async () => {
      await gf.stakeWallet(new PublicKey(farm.value!));
      await fetchFarmer();
      selectedNFTs.value = [];
    };

    const endStaking = async () => {
      await gf.unstakeWallet(new PublicKey(farm.value!));
      await fetchFarmer();
      selectedNFTs.value = [];
    };

    const claim = async () => {
      await gf.claimWallet(
        new PublicKey(farm.value!),
        new PublicKey(farmAcc.value.rewardA.rewardMint!),
        new PublicKey(farmAcc.value.rewardB.rewardMint!)
      );
      await fetchFarmer();
    };

    const handleRefreshFarmer = async () => {
      await fetchFarmer();
    };

    // --------------------------------------- adding extra gem
    const selectedNFTs = ref<INFT[]>([]);

    const handleNewSelectedNFT = (newSelectedNFTs: INFT[]) => {
      console.log(`selected ${newSelectedNFTs.length} NFTs`);
      selectedNFTs.value = newSelectedNFTs;
    };

    const addSingleGem = async (
      gemMint: PublicKey,
      gemSource: PublicKey,
      creator: PublicKey
    ) => {
      await gf.flashDepositWallet(
        new PublicKey(farm.value!),
        '1',
        gemMint,
        gemSource,
        creator
      );
      await fetchFarmer();
    };

    const addGems = async () => {
      await Promise.all(
        selectedNFTs.value.map((nft) => {
          const creator = new PublicKey(
            //todo currently simply taking the 1st creator
            (nft.onchainMetadata as any).data.creators[0].address
          );
          console.log('creator is', creator.toBase58());

          addSingleGem(nft.mint, nft.pubkey!, creator);
        })
      );
      console.log(
        `added another ${selectedNFTs.value.length} gems into staking vault`
      );
    };

    return {
      wallet,
      farm,
      farmAcc,
      farmer: farmerIdentity,
      farmerAcc,
      farmerState,
      availableA,
      availableB,
      initFarmer,
      beginStaking,
      endStaking,
      claim,
      handleRefreshFarmer,
      selectedNFTs,
      handleNewSelectedNFT,
      addGems,
    };
  },
});
</script>

<style scoped>
.wrap {
  max-width: 656px;
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
  padding: 16px;
  background: transparent;
  border: 1px solid #404040;
}

.farmerBtn {
  display: block;
  background: linear-gradient(90deg, #fbc7d4 0%, #9796f0 100%), #4aaf47;
  border-radius: 4px;
  max-width: 200px;
  width: 100%;
  max-height: 56px;
  padding: 13px;
}

/* @media  */

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
</style>
