<template lang='jade'>
  div#main
    iframe#ytplayer(
      :src='src', :width="width", :height="height" frameborder="0"
      allowfullscreen)
    div#description
      p(v-text='description')
</template>

<script lang='coffee'>
  export default
    props: ['src', 'description','width', 'height']

</script>

<style lang='stylus' scoped>
  #main
    width 100%
    height 100%

    display flex
    flex-wrap wrap

    justify-content center
    align-items center

    // border 1px solid red
    #ytplayer
      display block
    #description
      text-align center
</style>
