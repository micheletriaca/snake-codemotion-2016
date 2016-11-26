<template>
  <div class="ball" :style="{left: (position.x * chunkSize) + 'px', top: (position.y * chunkSize) + 'px'}"></div>
</template>
<script>
export default {
  props: {
    position: {
      type: Object,
      required: true
    },
    chunkSize: {
      default: 20
    }
  }
}
</script>
<style scoped>
  .ball {
    border-radius: 20px;
    background-color: green;
    width: 20px;
    height: 20px;
    position: absolute;
  }
</style>
