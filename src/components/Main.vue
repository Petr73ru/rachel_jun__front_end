<template lang='jade'>
  div#main
    iframe#ytplayer(
      :src='src', :width="width", :height="height" frameborder="0"
      allowfullscreen)
</template>

<script>
  export default {
    props: ['src', 'width', 'height']
  }
</script>

<style lang='stylus' scoped>
  #main
    width 100%
    height 100%
    display flex
    justify-content center
    align-items center
    border 1px solid red
  #ytplayer
    display block
  // TODO media-queries && paddings;
</style>
