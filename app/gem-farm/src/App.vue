<template>
  <div
    class="container mx-auto relative h-full min-h-screen flex flex-col justify-center align-middle"
  >
    <div class="flex justify-center align-middle mb-8">
      <p class="text-4xl text-white">Diamond Farm</p>
    </div>

    <div>
      <router-view />
    </div>
  </div>
</template>

<script>
// TXXfHyyABWbjrbyZkageC5B6sz77m9DEhWf2DrDX1w7s
export default {
  components: {},
};
</script>

<style>
input[type='radio']:checked + span {
  @apply text-black;
}
</style>
