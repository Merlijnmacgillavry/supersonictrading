<template>
  <div id="app" class="bg-dark">
    <navbar></navbar>
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import Navbar from './components/navbar'
export default {
  name: 'app',
  components: {
    HelloWorld,
      Navbar
  }
}
</script>

<style>
  #app{
    min-height: 100vh;

  }
*{
  margin: 0px;
  padding: 0px;
}
</style>
