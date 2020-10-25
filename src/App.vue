<template>
  <div id="app">
    <button @click="modifyData">modify dataset in parent</button>
    <BarChart :data-set="linearDataSet"/>
  </div>
</template>

<script>
// import LineChart from './components/LineChart'
import BarChart from './components/BarChart'

export default {
  name: 'App',
  components: {
    BarChart
  },
  data:() => ({
    linearDataSet : [32,17,57,29,54,23,52]
  }),
  methods:{
    modifyData() {
      this.linearDataSet = []
      for (let i=0;i < 7;i++) {
        let newNumber = Math.floor(Math.random() * 25)
        this.linearDataSet.push(newNumber)
      }
    }
  }
}
</script>

<style scoped>
 #app {
   text-align: center;
 }
</style>
