<template>
  <div class="settings">
    <div id="emotion">
 	    <h3>Selección del estado de ánimo:</h3>
      <select id="emotionSelect" v-model="emotion">
        <option value=2>Alegre</option>
        <option value=0>Neutro</option>
        <option value=1>Triste</option>
        <option value=3>Otro</option>
      </select>
    </div> 
    <div id="genre">
      <h3>Selección de los géneros musicales:</h3>
      <select id="genreSelect" v-model="genre">
        <option selected disabled>Seleccione un género...</option>
        <option value="02">Bachata</option>
        <option value="03">Balada</option>
        <option value="04">Bolero</option>
        <option value="05">Despecho</option>
        <option value="06">Electrónica</option>
        <option value="07">Jazz, Blues, Twist, Swing</option>
        <option value="08">Mambo</option>
        <option value="09">Merengue</option>
        <option value="10">Mexicana (Rancheras y Demás)</option>
        <option value="11">Clásica e Instrumental</option>
        <option value="12">Música Folclórica</option>
        <option value="13">Pop</option>
        <option value="14">Porro, Parrandera o tropical</option>
        <option value="15">Punk</option>
        <option value="16">Rap, Hip Hop</option>
        <option value="17">Reggae</option>
        <option value="18">Reggaeton</option>
        <option value="19">Rock (Cualquier Subgénero e Idioma)</option>
        <option value="20">Salsa</option>
        <option value="21">Social o Protesta</option>
        <option value="22">SoundTrack (Bandas Sonoras)</option>
        <option value="23">Tango</option>
        <option value="24">Vallenato</option>
        <option value="25">Cristiana</option>
        <option value="26">Bossanova</option>
      </select>
      <button v-on:click="changeGenres('insertar')">Registrar</button>
      <button v-on:click="changeGenres('eliminar')">Eliminar</button>
    </div>
    <br />
    <router-link to="/index" active-class="active"><a href="#" >Ir al reproductor</a></router-link>
  </div>
</template>

<script>
import axios from 'axios'
import querystring from 'querystring'
import router from '../router/index'

export default {
  data () {
    const token = localStorage.getItem('token')

    if (!token || token === '') {
      router.push('/index')
    }

    return {
      genre: 'Seleccione un género...',
      emotion: 0
    }
  },

  methods: {
    changeGenres: function (action = 'register') {
      if (this.genero === 'Seleccione un género...') return

      const userId = JSON.parse(localStorage.getItem('userId'))

      axios.post(`https://ludifica.com/mp7radio_test/servicios/actualizar-genero.php`,
        querystring.stringify({
          idUsuario: userId,
          idEstadoAnimo: this.emotion,
          codigoGenero: this.genre,
          accion: action
        })
      )
      .then((response) => { if (response.data.success) alert(response.data.mensaje + response.data.generos) })
      .catch((e) => { console.log(e) })
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
