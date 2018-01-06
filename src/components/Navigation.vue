<template lang='jade'>
  div#navigation
    v-btn(fab color='cyan' @click='btn')
      v-icon list
    v-navigation-drawer(temporary v-model='drawer' dark fixed)
      v-list
        v-list-tile(v-for='item in data', :key='item.contentDetails.videoId',
          @click=`itemSelected(item)`)
          v-list-tile-content(v-text='item.snippet.title')
</template>

<script lang='coffee'>
  export default
    props: ['data']
    data: ->
      drawer: off
    methods:
      btn: -> @drawer = not @drawer
      itemSelected: (item) ->
        @.$emit 'itemSelected', item
        @drawer = off
</script>

<style lang='stylus'>
  #navigation
    // border 1px solid green
</style>
