<template>
  <div class="login">
    <h1>Iniciar Sesión</h1>
      <b-form @submit="onSubmit">
        <b-form-group id="usuarioGroup"
                      label="Usuario" label-for="usuario">
          <b-form-input id="usuario"
                        type="text" required
                        placeholder="Ingrese su usuario"
          ></b-form-input>
        </b-form-group><br>
        <b-form-group id="contraseñaGroup"
                      label="Contraseña" label-for="contraseña">
          <b-form-input id="contraseña"
                        type="text" required
                        placeholder="Ingrese su contraseña"
          ></b-form-input>
        </b-form-group><br>
        <b-button type="submit" variant="primary">Ingresar</b-button>
      </b-form>
  </div>
</template>

<script>
export default {
  data: {
    form: {
      usuario: '',
      contraseña: '',
      secret: 'S3CR3T'
    }
  },
  methods: {
    onSubmit (evt) {
      evt.preventDefault()
      alert(JSON.stringify(this.form))
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