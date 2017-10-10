<template>
  <div class="signup">
    <h1>Regístrese</h1>
      <b-form @submit="signupPost">
        <b-form-group id="nameGroup"
                      label="Nombre" label-for="name">
          <b-form-input id="name"
                        type="text" required
                        placeholder="Ingrese su nombre"
                        v-model="postBody.nombre"
          ></b-form-input>
        </b-form-group><br>
        <b-form-group id="userGroup"
                      label="Usuario" label-for="user">
          <b-form-input id="user"
                        type="text" required
                        placeholder="Ingrese su usuario"
                        v-model="postBody.usuario"
                        :formatter="formatLowercase"
          ></b-form-input>
        </b-form-group><br>
        <b-form-group id="passwordGroup"
                      label="Contraseña" label-for="password">
          <b-form-input id="password"
                        type="password" required
                        placeholder="Ingrese su contraseña"
                        v-model="postBody.clave"
          ></b-form-input>
        </b-form-group><br>
        <b-form-group id="emailGroup"
                      label="Correo Electrónico" label-for="email">
          <b-form-input id="email"
                        type="email" required
                        placeholder="Ingrese su correo electrónico"
                        v-model="postBody.correo"
          ></b-form-input>
        </b-form-group><br>
        <b-button type="submit" variant="primary">Registrarse</b-button>
      </b-form>
  </div>
</template>

<script>

import axios from 'axios'
import querystring from 'querystring'

export default {
  data () {
    return {
      postBody: {
        nombre: '',
        usuario: '',
        clave: '',
        correo: '',
        genero: 'M'
      },
      errors: []
    }
  },
  methods: {
    formatLowercase (value, event) {
      return value.toLowerCase()
    },
    signupPost () {
      axios.post(`http://aidia-e.com/mp7radio_test/servicios/registrar-usuario.php`,
        querystring.stringify({...this.postBody})
      )
      .then(response => { console.log(response) })
      .catch(e => {
        this.errors.push(e)
      })
    },
    onSubmit (evt) {
      evt.preventDefault()
      alert(JSON.stringify(this.postBody))
    }
  }
}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
