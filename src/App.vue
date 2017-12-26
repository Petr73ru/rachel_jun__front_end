<template lang='jade'>
  v-app(dark)
    app-header
    app-navigation(:data='channelData', @itemSelected='changeVideoId')
    app-main(:src='src', :width='width', :height='height')
</template>
<!-- 320:180, 480:270, 720:405, 1080:608 -->
<script>
import axios from 'axios'
import Header from './components/Header.vue'
import Navigation from './components/Navigation.vue'
import Main from './components/Main.vue'
export default {
  data () {
    return {
      channelData: [],
      src: null,
      width: null,
      embed: 'https://www.youtube.com/embed/',
      brand: '?modestbranding=1&controls=0&rel=0&showinfo=0'
    }
  },
  computed: {
    height () {
      let width = this.$vuetify.breakpoint.width
      if (width >= 320) this.width = 320
      if (width >= 480) this.width = 480
      if (width >= 720) this.width = 720
      if (width >= 1080) this.width = 1080
      let height = Math.floor(this.width / 1.777)
      return height
    }
  },
  methods: {
    changeVideoId (id) {
      this.src = this.embed + id + this.brand
    },
    getPlaylistId (channelId) {
      // channelId to playlist 'uploaded' id
      // 'UCOgGAfSUy5LvEyVS_LF5kdw' to 'UUOgGAfSUy5LvEyVS_LF5kdw'
      // 'UU...' to 'UC...'
    },
    createLink (playlistId, pageToken) {
      let url = 'https://www.googleapis.com/youtube/v3/playlistItems'
      let query = [
        'fields=items(contentDetails(videoId%2CvideoPublishedAt)%2Csnippet(description%2Ctitle))%2CnextPageToken%2CpageInfo%2CprevPageToken',
        'part=snippet%2CcontentDetails',
        'maxResults=50',
        pageToken ? 'pageToken=' + pageToken : null,
        playlistId ? 'playlistId=' + playlistId : null,
        'key=AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY'
      ]
      console.log(url + '?' + query.join('&'))
      axios(url + '?' + query.join('&')).then(res => {
        this.channelData = this.channelData.concat(res.data.items)
        this.src === null
          ? this.src = this.embed + this.channelData[0].contentDetails.videoId + this.brand
          : null
        res.data.nextPageToken
          ? this.createLink(playlistId, res.data.nextPageToken)
          : null
      })
    }
  },
  components: {
    appHeader : Header,
    appNavigation : Navigation,
    appMain : Main
  },
  created () {
    let Jolly = 'UUOgGAfSUy5LvEyVS_LF5kdw' // playlist ID
    let Shifman = 'UUvjgXvBlbQiydffZU7m1_aw'
    let GeeksforGeeks = 'UU0RhatS1pyxInC00YKjjBqQ'
    this.createLink(GeeksforGeeks)
  }
}

</script>
