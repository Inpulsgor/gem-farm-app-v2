<template>
  <button class="btn" :disabled="disabled">
    <img
      src="../assets/up.svg"
      :alt="left ? 'left' : 'right'"
      :width="32"
      :height="16"
      :class="{ rightMobile: !left, disabled: disabled }"
    />
  </button>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
export default defineComponent({
  props: {
    left: Boolean,
    disabled: Boolean,
  },
});
</script>

<style scoped>
button,
img {
  outline: none !important;
}
.left {
  transform: scaleX(-1);
}
.rightMobile {
  transform: scaleY(-1);
}
.disabled {
  opacity: 0.1;
}
.btn:hover {
  cursor: pointer;
}
</style>
