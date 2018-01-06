<template lang="jade">
  div#header
    h1(v-text='title')
</template>

<script lang='coffee'>
export default  
  props: ['title']
  
</script>

<style lang="stylus">
  #header
    width 100%
    height 6em
    text-align center
    // border 1px solid blue
  h1
   color orange
   margin-top 0.4em
  h2
    color white
</style>
