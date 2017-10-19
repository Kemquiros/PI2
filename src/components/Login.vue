<template>
  <div class="login">
    <h1>Iniciar Sesión</h1>
      <b-form @submit="loginPost">
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
        <b-button type="submit" variant="primary">Ingresar</b-button>
      </b-form>
  </div>
</template>

<script>

import axios from 'axios'
import md5 from 'md5'
import querystring from 'querystring'
import router from '../router/index'

export default {
  data () {
    return {
      postBody: {
        usuario: '',
        clave: '',
        tokenusuario: ''
      },
      errors: []
    }
  },
  methods: {
    formatLowercase (value, event) {
      return value.toLowerCase()
    },
    loginPost () {
      const { usuario, clave, tokenusuario } = this.postBody
      axios.post(`http://aidia-e.com/mp7radio_test/servicios/validar-usuario.php`,
        querystring.stringify({
          usuario: md5(usuario),
          clave: md5(clave),
          tokenusuario
        })
      )
      .then(response => {
        // JSON responses are automatically parsed.
        if (response.data.success === true) {
          localStorage.setItem('id', JSON.stringify(response.data.id))
          localStorage.setItem('name', JSON.stringify(response.data.nombre))
          localStorage.setItem('token', JSON.stringify(response.data.tokenusuario))
          router.push('/index')
        } else {
          alert('Datos incorrectos.')
        }
      })
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
