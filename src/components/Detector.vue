<template>
  <div class="detector">
    <video id="video" ref="video" autoplay="true" v-bind:src="videoSrc"></video>
    <br />
    <button id="snapButton" ref="snapButton" disabled="true" v-on:click="this.onSnapClick">Loading detector</button>
    <br />
    <canvas id="canvas" ref="canvas" width="0" height="0"></canvas>
  </div>
</template>

<script>
/* global affdex */
export default {
  data () {
    return {
      detector: null,
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
      const faceMode = affdex.FaceDetectorMode.SMALL_FACES
      this.detector = new affdex.PhotoDetector(faceMode)
      this.detector.detectAllEmotions()
      this.detector.detectAllExpressions()

      // Acciones cuando el detector de inicia correctamente o falla
      this.detector.addEventListener('onInitializeSuccess', this.onInitializeSuccess)
      this.detector.addEventListener('onInitializeFailure', () => {
        this.handleError('initialize failed')
      })

      // Acciones cuando el detector procese la imagen correctamente o falla
      this.detector.addEventListener('onImageResultsSuccess', this.onImageResultsSuccess)
      this.detector.addEventListener('onImageResultsFailure', (image, timestamp, err) => {
        this.handleError(err)
      })

      this.detector.start()
    },
    handleError: function (err) {
      console.log(err)
    },
    onSnapClick: function (e) {
      const { videoWidth, videoHeight } = this.$refs.video
      const context = this.$refs.canvas.getContext('2d')
      this.$refs.canvas.width = videoWidth
      this.$refs.canvas.height = videoHeight
      context.drawImage(this.$refs.video, 0, 0, videoWidth, videoHeight)
      const imageData = context.getImageData(0, 0, videoWidth, videoHeight)

      if (this.detector && this.detector.isRunning) {
        this.detector.process(imageData, 0)
      }
    },
    onInitializeSuccess: function () {
      this.$refs.snapButton.innerHTML = 'Take Photo'
      this.$refs.snapButton.disabled = false
    },
    onImageResultsSuccess: function (faces, image, timestamp) {
      console.log(faces, image, timestamp)
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
