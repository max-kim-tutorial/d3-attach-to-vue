<template>
  <div id="app">
    <LineChart :data-set="dataSet"/>
    <button @click="modifyData">modify dataset in parent</button>
  </div>
</template>

<script>
import LineChart from './components/LineChart'

export default {
  name: 'App',
  components: {
    LineChart
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
    }
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
