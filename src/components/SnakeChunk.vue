<template>
  <div class="snake" :style="{left: (position.left * chunkSize) + 'px', top: (position.top * chunkSize) + 'px'}">
  </div>
</template>
<script>
export default {
  props: {
    position: {
      required: true
    },
    chunkSize: {
      default: 20
    }
  }
}
</script>
<style scoped>
  .snake {
    background-color: red;
    border-radius: 5px;
    height: 20px;
    width: 20px;
    position: absolute;
    z-index: 10;
  }
</style>
