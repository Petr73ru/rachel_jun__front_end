<template lang='jade'>
  v-app(dark)
    app-header(:title='title')
    app-navigation(:data='videos', @itemSelected='changeVideoId')
    app-main(:src='src', :description='description',
             :width='width', :height='height')
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
      GoogleAuth: null,
      videos: [],
      subChannels: [],

      src: null,
      width: null,
      embed: 'https://www.youtube.com/embed/',
      brand: '?modestbranding=1&controls=0&rel=0&showinfo=0',

      title: '',
      description: ''
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
    changeVideoId (item) {
      this.src = this.embed + item.contentDetails.videoId + this.brand
      this.title = item.snippet.title
      this.description = item.snippet.description
    },
    getChannelVideos (playlistId, pageToken) {
      let url = 'https://www.googleapis.com/youtube/v3/playlistItems'
      let query = [
        'fields=items(contentDetails(videoId%2CvideoPublishedAt)%2Csnippet'
          + '(description%2Ctitle))'
          + '%2CnextPageToken%2CpageInfo%2CprevPageToken',
        'part=snippet%2CcontentDetails',
        'maxResults=50',
        pageToken ? 'pageToken=' + pageToken : null,
        playlistId ? 'playlistId=' + playlistId : null,
        'key=AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY'
      ]
      url = url + '?' + query.join('&')
      axios(url).then(res => {
        this.videos = this.videos.concat(res.data.items)
        if (this.src === null) {
          this.src = this.embed + this.videos[0]
            .contentDetails.videoId + this.brand
          this.title = this.videos[0].snippet.title
          this.description = this.videos[0].snippet.description
        }
        res.data.nextPageToken
          ? this.getChannelVideos(playlistId, res.data.nextPageToken)
          : null
      })
    },
    getChannels (channelId, pageToken) {
      let url = 'https://www.googleapis.com/youtube/v3/subscriptions'
      let query = [
        'fields=items(snippet(channelTitle%2Cdescription%2CresourceId%2F'
          + 'channelId%2Ctitle))%2CnextPageToken%2CpageInfo%2CprevPageToken',
        'part=snippet%2CcontentDetails',
        'maxResults=50',
        pageToken ? 'pageToken=' + pageToken : null,
        channelId ? 'channelId=' + channelId : null,
        'key=AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY'
      ]
      // console.log(url + '?' + query.join('&'))
      axios(url + '?' + query.join('&')).then(res => {
        console.log(res)
        this.subChannels = this.subChannels.concat(res.data.items)
        res.data.nextPageToken
          ? this.getChannels(channelId, res.data.nextPageToken)
          : null
      })
    },
    initClient () {
      console.log('init : OK')
      window.gapi.client.init({
        apiKey         : 'AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY',
        clientId       : '689095975783-frg9n0kk1a09nu5he6d5sr17a7fo0idc.apps.googleusercontent.com',
        scope          : 'https://www.googleapis.com/auth/youtube',
        discoveryDocs  : ["https://www.googleapis.com/discovery/v1/apis/youtube/v3/rest"]
      }).then(() => {
        this.GoogleAuth = window.gapi.auth2.getAuthInstance()
        this.GoogleAuth.isSignedIn.listen(() => { console.log('LOL: I\'m signed in!') } )
        this.GoogleAuth.signIn()
      }).catch( er => console.warn(er))
    }
  },
  components: {
    appHeader : Header,
    appNavigation : Navigation,
    appMain : Main
  },
  created () {
    let cleverprogrammer = 'UUqrILQNl5Ed9Dz6CGMyvMTQ'
    let Stefan = 'UUyUBW72KU30dfAYWLVNZO8Q'
    let SeniorSoftware = 'UUX3w3jB05SHLbGjZPR0PM6g'
    this.getChannelVideos(Stefan)
    window.gapi.load('client:auth2', this.initClient)
    // this.getChannels ('UCFyPeu3CTyy9ImKl4OvJgbw') // my channel id
  }
}

</script>
