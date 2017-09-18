<template>
  <div class="signup">
    <h1>Regístrese</h1>
      <b-form @submit="onSubmit">
        <b-form-group id="userGroup"
                      label="Usuario" label-for="user">
          <b-form-input id="user"
                        type="text" required
                        placeholder="Ingrese su usuario"
          ></b-form-input>
        </b-form-group><br>
        <b-form-group id="passwordGroup"
                      label="Contraseña" label-for="password">
          <b-form-input id="password"
                        type="text" required
                        placeholder="Ingrese su contraseña"
          ></b-form-input>
        </b-form-group><br>
        <b-button type="submit" variant="primary">Registrarse</b-button>
      </b-form>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      form: {
        user: '',
        password: ''
      }
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