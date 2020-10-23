<template>
  <div class='bar-chart' />
</template>

<script>
import d3 from 'd3'

export default {
  name:'BarChart',
  data:() => ({
    width: 500,
    height: 500,
    margin: { top:40, right:40, bottom:40, left:40 },
    data: [
      {
        id: 0,
        date: new Date('03-01'),
        value: 400,
      },
       {
        id: 1,
        date: new Date('03-02'),
        value: 165,
      },
       {
        id: 2,
        date: new Date('03-03'),
        value: 650,
      },
      {
        id: 3,
        date: new Date('03-04'),
        value: 870,
      },
    ],
  }),
  methods: {
    drawChart() {
      // x축 : 도메인 설정
      const x = d3.scaleTime()
      .domain(d3.extent(this.data, d => d.date))
      .range([margin.left, width - margin.right])
    }
  }
}
</script>

<style scoped>

</style>