<template>
  <div id="app">
    <button @click="addDataSet">add</button>
    <button @click="removeDataSet">remove</button>
    <button @click="changeDataSet">change</button>
    <BarChart :data-set="dataSet"/>
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
    linearDataSet : [32,17,57,29,54,23,52],
    dataSet: [
      {label:'광주', value:32},
      {label:'서울', value:25},
      {label:'대구', value:12},
      {label:'부산', value:31},
      {label:'울산', value:47},
      {label:'인천', value:28},
      {label:'대전', value:21},
      {label:'창원', value:19},
    ]
  }),
  methods:{
    modifyLinearData() {
      this.linearDataSet = []
      for (let i=0;i < 7;i++) {
        let newNumber = Math.floor(Math.random() * 25)
        this.linearDataSet.push(newNumber)
      }
    },
    addDataSet() {
      this.dataSet = [
        ...this.dataSet,
        {label:'전주', value:Math.floor(Math.random() * 25)}
      ]
    },
    removeDataSet() {
      this.dataSet = this.dataSet.filter((data, index) => index !== 0)
    },
    changeDataSet() {
      const dataLength = this.dataSet.length
      this.dataSet = []
      for(let i = 0; i < dataLength; i++) {
        this.dataSet = [
          ...this.dataSet, 
          {label:`도시${i}`, value:Math.floor(Math.random() * 25)}
        ]
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
