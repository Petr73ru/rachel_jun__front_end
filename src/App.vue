<template lang='jade'>
  v-app(dark)
    app-header(:title='title')
    app-navigation(:data='videos', @itemSelected='changeVideoId')
    app-main(:src='src', :description='description',
             :width='width', :height='height')
</template>
<!-- 320:180, 480:270, 720:405, 1080:608 -->
<script lang='coffee'>
  import axios from 'axios'
  import Header from './components/Header.vue'
  import Navigation from './components/Navigation.vue'
  import Main from './components/Main.vue'
  export default
    data: ->
      GoogleAuth: null
      videos: []
      subChannels: []

      src: null
      width: 320
      embed: 'https://www.youtube.com/embed/'
      brand: '?modestbranding=1&controls=0&rel=0&showinfo=0'

      title: ''
      description: ''
      
    created: ->
      cleverprogrammer = 'UUqrILQNl5Ed9Dz6CGMyvMTQ'
      Stefan = 'UUyUBW72KU30dfAYWLVNZO8Q'
      SeniorSoftware = 'UUX3w3jB05SHLbGjZPR0PM6g'
      Winderton = 'UU4omkhNHsYLagT1o6hnmKQw'

      @getChannelVideos Winderton
      # console.debug 'Debug'
      # @initClient()
      window.gapi.load 'client:auth2', @initClient
      
    computed:
        
      height: ->
        width = @$vuetify.breakpoint.width
        @width = 480 if width >= 480
        @width = 720 if width >= 720
        @width = 1080 if width >= 1080
        @width = 320 if width < 480
        height = Math.floor @width / 1.777
	
    methods:
      changeVideoId: (item) ->
        @src = @embed + item.contentDetails.videoId + @brand
        @title = item.snippet.title
        @description = item.snippet.description
	
      getChannelVideos: (playlistId='', pageToken='') ->
        url = 'https://www.googleapis.com/youtube/v3/playlistItems'
        query = [
          'fields=items(contentDetails(videoId%2CvideoPublishedAt)%2Csnippet(description%2Ctitle))%2CnextPageToken%2CpageInfo%2CprevPageToken'
          'part=snippet%2CcontentDetails'
          'maxResults=50'
          if pageToken? then "pageToken=#{pageToken}" else null
          if playlistId? then "playlistId=#{playlistId}" else null
          'key=AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY'
        ]
        url = "#{url}?#{query.join '&'}"
        axios(url).then (res) =>
          console.log res.data.items
          @videos = @videos.concat res.data.items
          unless @src?
            @src = @embed + @videos[0].contentDetails.videoId + @brand
            @title = @videos[0].snippet.title
            @description = @videos[0].snippet.description
          @getChannelVideos playlistId, res.data.nextPageToken if res.data.nextPageToken?
	  
      getChannels: (channelId, pageToken) ->
        url = 'https://www.googleapis.com/youtube/v3/subscriptions'
        query = [
          'fields=items(snippet(channelTitle%2Cdescription%2CresourceId%2F'
            + 'channelId%2Ctitle))%2CnextPageToken%2CpageInfo%2CprevPageToken'
          'part=snippet%2CcontentDetails'
          'maxResults=50'
          "pageToken=#{pageToken}" if pageToken?
          "channelId=#{channelId}" if channelId?
          'key=AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY'
        ]
        # axios("#{url}?#{query.join '&'}").then (res) ->
        #   console.log res
        #   this.subChannels = this.subChannels.concat res.data.items
        #   this.getChannels channelId, res.data.nextPageToken if res.data.nextPageToken?
	
      initClient: ->
        console.log 'init : OK'
        window.gapi.client.init
          apiKey         : 'AIzaSyAEc6reXtKwVemgtV9MypLcOHE2dnovLMY'
          clientId       : '689095975783-frg9n0kk1a09nu5he6d5sr17a7fo0idc.apps.googleusercontent.com'
          scope          : 'https://www.googleapis.com/auth/youtube'
          discoveryDocs  : ["https://www.googleapis.com/discovery/v1/apis/youtube/v3/rest"]
        .then =>
          @GoogleAuth = window.gapi.auth2.getAuthInstance()
          @GoogleAuth.isSignedIn.listen ->
            if @GoogleAuth.isSignedIn.get() then console.log 'I\'m signed in!' else console.log 'I\'m log out!'
          @GoogleAuth.signIn() unless @GoogleAuth.isSignedIn.get()
        .catch (er)-> console.warn er
	
    components:
      appHeader : Header
      appNavigation : Navigation
      appMain : Main





</script>
