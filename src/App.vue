<template>
  <div id="app">
    <LineChart/>
  </div>
</template>

<script>
import LineChart from './components/LineChart'

export default {
  name: 'App',
  components: {
    LineChart
  }
}
</script>

<style scoped>

#app {
  display: flex;
  justify-content: center;
  align-items: center;
}

</style>
