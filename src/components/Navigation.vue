<template lang='jade'>
  div#navigation
    v-btn(fab color='cyan' @click='btn')
      v-icon list
    v-navigation-drawer(temporary v-model='drawer' dark fixed)
      v-list
        v-list-tile(v-for='item in data' key='item.id.videoId'
          @click='itemSelected(item.id.videoId)')
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
      itemSelected(id) {
        this.$emit('itemSelected', id)
        this.drawer = false
      }
    }
  }
</script>


<!-- methods: {
  itemSelected (item) {
    this.src = this.embed + item.id.videoId + this.brand
    this.drawer = false
  },
  btn () {
    this.drawer = !this.drawer
  } -->
