<template>
  <div class="home">
    <h1>{{ msg }}</h1>
    <h2>Música para tu estado de Animo</h2>
    <ul>
      <router-link to="/login" active-class="active"  tag="li"><a href="#">Iniciar Sesión</a></router-link>
      <router-link to="/signup" active-class="active"  tag="li"><a href="#" >Registrarse</a></router-link>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'home',
  data () {
    return {
      msg: 'Bienvenid@ a MP7'
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
