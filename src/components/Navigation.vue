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

<script>
  export default {
    props: ['data'],
    data () {
      return {
        drawer: false
      }
    },
    methods: {
      btn () {
        this.drawer = !this.drawer
      },
      itemSelected(item) {
        this.$emit('itemSelected', item)
        this.drawer = false
      }
    }
  }
</script>

<style lang='stylus'>
  #navigation
    // border 1px solid green
</style>
