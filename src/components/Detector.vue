<template>
  <div class="detector">
    <video id="video" ref="video" autoplay="true" v-bind:src="videoSrc"></video>
    <br />
    <button id="snapButton" ref="snapButton" disabled="true" v-on:click="this.onSnapClick">{{snapButtonText}}</button>
    <br />
    <canvas class="canvas" v-show="showCanvas" id="canvas" ref="canvas" ></canvas>
    </div>
  </div>
</template>

<script>
/* global affdex */
import axios from 'axios'
import querystring from 'querystring'

export default {
  data () {
    return {
      detector: null,
      videoSrc: null,
      snapButtonText: 'Loading detector',
      showCanvas: false
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

      this.$refs.canvas.width = videoWidth
      this.$refs.canvas.height = videoHeight
      this.showCanvas = true

      const context = this.$refs.canvas.getContext('2d')
      context.drawImage(this.$refs.video, 0, 0, videoWidth, videoHeight)
      const imageData = context.getImageData(0, 0, videoWidth, videoHeight)

      if (this.detector && this.detector.isRunning) {
        this.detector.process(imageData, 0)
      }
    },
    onInitializeSuccess: function () {
      this.snapButtonText = 'Take Photo'
      this.$refs.snapButton.disabled = false
    },
    onImageResultsSuccess: function (faces, image, timestamp) {
      const emotion = this.getEmotion(faces)
      console.log('emotion', emotion)
      this.getSongList(emotion).then((list) => this.$emit('songListGot', list))
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
          /* 0 - Neutral
          1 - Tristeza
          2 - Alegría
          3 - Otro
          */

          case 'joy': {
            console.log('joy')
            return 2
          }
          case 'sadness': {
            console.log('sadness')
            return 1
          }
          case 'disgust': {
            console.log('disgust')
            return 1
          }
          case 'contempt': {
            console.log('contempt')
            return 1
          }
          case 'anger': {
            console.log('anger')
            return 3
          }
          case 'fear': {
            console.log('fear')
            return 3
          }
          case 'surprise': {
            console.log('surprise')
            return 3
          }
        }
      }

      return 3
    },
    getSongList: async function (emotion, size = 10) {
      const list = []
      for (let i = 0; i < size; i += 1) {
        await this.getNextTrack(emotion)
          .then((data) => list.push(data))
          .catch((e) => this.handleError(e))
      }

      return list
    },
    getNextTrack: async function (emotion) {
      const userId = JSON.parse(localStorage.getItem('userId'))

      try {
        const response = await axios.post(`https://ludifica.com/mp7radio_test/servicios/seleccionar-audio.php`,
          querystring.stringify({
            idUsuario: userId,
            idEstadoAnimo: emotion,
            generos: '',
            idAudio: 0,
            nroCanciones: 0,
            tiempo: 0
          })
        )

        return response.data
      } catch (e) {
        return e
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.detector {
  float: left;
  width: 50%;
}
.canvas {
  width: 20%;
  height: 20%;
}

h1,
h2 {
  font-weight: normal;
}

a {
  color: #42b983;
}
</style>
