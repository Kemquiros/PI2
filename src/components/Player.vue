<template>
  <div>
    <div class="emotion" v-show="showEmotion">
      <p>Emoción detectada</p>
      <p v-html="emotion"></p>
    </div>
    <div class="player" v-show="showPlayer">
      <div v-for="song in list" v-bind:key="song.id" v-on:click="onSongClick($event, song)">
          <p>{{song.grupoartista}} - {{song.titulo}}</p>
      </div>
      <audio id="audio" ref="audio" controls>
        <source v-bind:src="mpegSrc" type="audio/mpeg">
        Your browser does not support the audio element.
      </audio>
    </div>
  </div>
</template>

<script>

export default {
  props: ['emotion', 'list'],
  data () {
    return {
      mpegSrc: 'https://aidia-e.com/mydj/musica/1/361.mp3',
      showPlayer: false,
      showEmotion: false
    }
  },

  beforeUpdate () {
    if (this.list.length) {
      this.showPlayer = true
    }

    if (this.emotion !== '') {
      this.showEmotion = true
    }
  },

  methods: {
    handleError: function (err) {
      console.log(err)
    },
    onSongClick: function (event, song) {
      this.mpegSrc = song.rutaMp3
      this.$refs.audio.load()
      this.$refs.audio.play()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.player {
  display: inline-block;
}
h1,
h2 {
  font-weight: normal;
}

a {
  color: #42b983;
}
</style>
