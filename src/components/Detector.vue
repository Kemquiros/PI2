<template>
  <div class="detector">
    <video id="video" ref="video" autoplay="true" v-bind:src="videoSrc"></video>
    <br />
    <button id="snapButton" ref="snapButton" disabled="true" v-on:click="this.onSnapClick">Loading detector</button>
    <br />
    <canvas v-show="showCanvas" id="canvas" ref="canvas" v-bind:width="videoWidth" v-bind:height="videoHeight"></canvas>
    </div>
  </div>
</template>

<script>
/* global affdex */
export default {
  data () {
    return {
      detector: null,
      videoSrc: null,
      showCanvas: false,
      videoWidth: 0,
      videoHeight: 0
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

      this.videoWidth = videoWidth
      this.videoHeight = videoHeight
      this.showCanvas = true

      const context = this.$refs.canvas.getContext('2d')
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
      const emotion = this.getEmotion(faces)
      console.log(emotion)
    },
    getEmotion: function (faces) {
      // Si se detectó al menos una cara
      if (!faces.length) {
        return null
      }

      const higherEmotion = {
        emotion: '',
        level: 0
      }

      faces.forEach(function (face) {
        for (var emotion in face.emotions) {
          // Ignorar valence and engagement (no son emociones en sí)
          if (emotion !== 'valence' && emotion !== 'engagement') {
            const level = face.emotions[emotion]

            if (level > higherEmotion.level) {
              higherEmotion.emotion = emotion
              higherEmotion.level = level
            }
          }
        };
      })

      if (higherEmotion.level >= 1) {
        switch (higherEmotion.emotion) {
          case 'joy': {
            return 'alegría'
          }
          case 'sadness': {
            return 'tristeza'
          }
          case 'disgust': {
            return 'disgusto'
          }
          case 'contempt': {
            return 'contemplacion'
          }
          case 'anger': {
            return 'enojo'
          }
          case 'fear': {
            return 'miedo'
          }
          case 'surprise': {
            return 'sorpresa'
          }
        }
      }

      return 'neutra'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}

a {
  color: #42b983;
}
</style>
