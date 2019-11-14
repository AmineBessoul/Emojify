<template>
  <div id="app">
    <Main />
  </div>
</template>

<script>
import Main from './components/Main';
export default {
  name: 'app',
  components: {
    Main
  }
}
</script>

<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }
  body {
    font-family: Arial, Helvetica, sans-serif;
    line-height: 1.4;
  }
</style>