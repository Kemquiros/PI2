<template>
  <div class="detector">
    <video id="video" autoplay="true" v-bind:src="videoSrc"></video>
    <br />
    <button id="snapButton" ref="snapButton" disabled="true" v-on:click="this.onSnapClick">Loading detector</button>
  </div>
</template>

<script>
export default {
  data () {
    return {
      videoSrc: null
    }
  },

  mounted () {
    this.setUp()
  },

  methods: {
    setUp: function () {
      if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        navigator.mediaDevices.getUserMedia({ video: true })
          .then((stream) => {
            this.videoSrc = window.URL.createObjectURL(stream)
            this.startDetector()
          })
          .catch((err) => {
            this.handleError(err)
          })
      } else if (navigator.getUserMedia) { // Si mediaDevices no funciona para el navegador. Usar forma Standard
        navigator.getUserMedia({ video: true }, (stream) => {
          this.videoSrc = stream
          this.startDetector()
        }, this.handleError)
      } else if (navigator.webkitGetUserMedia) { // WebKit-prefixed
        navigator.webkitGetUserMedia({ video: true }, (stream) => {
          this.videoSrc = window.webkitURL.createObjectURL(stream)
          this.startDetector()
        }, this.handleError)
      } else if (navigator.mozGetUserMedia) { // Mozilla-prefixed
        navigator.mozGetUserMedia({ video: true }, (stream) => {
          this.videoSrc = window.URL.createObjectURL(stream)
          this.startDetector()
        }, this.handleError)
      }
    },
    startDetector: function () {
      this.$refs.snapButton.innerHTML = 'Take Photo'
      this.$refs.snapButton.disabled = false
    },
    handleError: function (err) {
      console.log(err)
    },
    onSnapClick: function (e) {
      console.log(e)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

a {
  color: #42b983;
}
</style>
