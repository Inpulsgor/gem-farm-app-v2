<template>
  <div class="faq with-title">
    <p class="text-base text-white mb-5">{{ title }}</p>
    <slot />
    <div class="flex flex-wrap">
      <NFTCard
        v-for="nft in nfts"
        :key="nft"
        :nft="nft"
        @selected="handleSelected"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import NFTCard from '@/components/gem-bank/NFTCard.vue';

export default defineComponent({
  components: { NFTCard },
  emits: ['selected'],
  props: {
    title: String,
    nfts: Array,
  },
  setup(props, ctx) {
    const handleSelected = (e: any) => {
      ctx.emit('selected', e);
    };
    return {
      handleSelected,
    };
  },
});
</script>

<style scoped>
.faq {
  border: 1px solid #1d1d1d;
  height: max-content;
  color: #a1a1a1;
}
</style>
