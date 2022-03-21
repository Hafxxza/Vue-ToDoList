<template>
  <div id="app" class="container">
    <div class="row">
      <Home/>
    </div>
  </div>
</template>

<script>
import Home from '@/components/Home.vue'

export default {
  name: 'App',
  components: {
    Home
  }
}

</script>
    Home

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
  font-size: 18px;
  border: 1px solid black;
  border-radius: 20px;
  padding: 20px;
  background-color: ghostwhite;
}
</style>
