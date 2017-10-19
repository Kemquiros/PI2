<template>
  <div class="index">
    <h1>Bienvenido {{name}}</h1>
    <button v-on:click="onSignOut">Sign Out</button>
    <p>Label Test</p>     

  </div>
</template>

<script>
import router from '../router/index'

export default {
  data () {
    const token = localStorage.getItem('token')

    if (!token || token === '') {
      router.push('/')
    }

    return {
      name: localStorage.getItem('name')
    }
  },

  methods: {
    onSignOut: () => {
      localStorage.removeItem('token')
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
