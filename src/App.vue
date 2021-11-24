<template>
  <div class="wrapper">
    <projectsList />
  </div>
</template>

<script>
import projectsList from './components/ProjectsList.vue'

export default {
  name: 'App',
  components: {
    projectsList
  }
}

</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;500&display=swap');

body {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

#app {
  font-family: 'Open Sans', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.wrapper {
  max-width: 1280px;
  margin: auto;
  padding: 0 15px;
  box-sizing: border-box;
}
</style>
