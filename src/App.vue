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
      src: '',
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
    }
  },
  components: {
    appHeader : Header,
    appNavigation : Navigation,
    appMain : Main
  },
  created () {
    axios('https://rachel-jun-channel.herokuapp.com/')
      .then(res => {
        this.src = this.embed + res.data[0].id.videoId + this.brand
        return this.channelData = res.data
      })
    let Jolly = 'https://www.googleapis.com/youtube/v3/playlistItems?part=snippet%2CcontentDetails&maxResults=50&pageToken=CDIQAQ&playlistId=UUOgGAfSUy5LvEyVS_LF5kdw&fields=items(contentDetails(videoId%2CvideoPublishedAt)%2Csnippet(description%2Ctitle))%2CnextPageToken%2CpageInfo%2CprevPageToken&key=AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY'
    axios(Jolly).then(console.log)
  }
}

</script>
