<template>
  <div class="index">
      <h1>Bienvenido {{name}}</h1>
      <div>
        <router-link to="/settings" active-class="active"><a href="#">Preferencias</a></router-link>
        <button v-on:click="onSignOut">Sign Out</button>
      </div>
      <Detector v-on:songListGot="onSongListGot" v-on:emotionGot="onEmotionGot"></Detector>
      <Player v-bind:list="this.songList" v-bind:emotion="this.emotion"></Player>
  </div>
</template>

<script>
import router from '../router/index'
import Detector from './Detector.vue'
import Player from './Player.vue'

export default {
  data () {
    const token = JSON.parse(localStorage.getItem('token'))

    if (!token || token === '') {
      router.push('/')
    }

    return {
      name: JSON.parse(localStorage.getItem('name')),
      songList: [],
      emotion: ''
    }
  },

  methods: {
    onSignOut: function () {
      localStorage.removeItem('token')
      router.push('/')
    },
    onSongListGot: function (songList) {
      this.songList = songList
    },
    onEmotionGot: function (emotion) {
      this.emotion = emotion
    }
  },

  components: { Detector, Player }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
  margin-bottom: 0px;
}

a {
  color: #42b983;
}
</style>
