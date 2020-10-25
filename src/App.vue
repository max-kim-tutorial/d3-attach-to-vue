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
    dataSet: [
      {name: 'a', value: 10},
      {name: 'b', value: 29},
      {name: 'c', value: 32},
      {name: 'd', value: 25},
      {name: 'e', value: 23},
      {name: 'f', value: 15}
    ],
    linearDataSet : [32,17,57,29,54,23,52]
  }),
  methods:{
    modifyData() {
      this.dataSet = [
        {name: 'a', value: 24},
        {name: 'b', value: 29},
        {name: 'c', value: 25},
        {name: 'd', value: 23},
        {name: 'e', value: 21},
        {name: 'f', value: 51},
        {name: 'g', value: 71},
      ]
      this.linearDataSet = [22, 33, 15, 23, 52, 24, 34]
    }
  }
}
</script>

<style scoped>
 #app {
   text-align: center;
 }
</style>
