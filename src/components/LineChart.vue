<template>
  <div>
    <button @click="makeChart">chart</button>
    <button @click="dataModify">data modify</button>
    <section class='line-chart' />
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name:'LineChart',
  data:() => ({
    width: 500,
    height: 500,
    margin: { top:40, right:40, bottom:40, left:40 },
    dataSet: [
      {name: 'a', value: 10},
      {name: 'b', value: 29},
      {name: 'c', value: 32},
      {name: 'd', value: 25},
      {name: 'e', value: 23},
      {name: 'f', value: 15}
    ],
  }),
  methods: {
    makeChart() {
      this.drawChart()
    },
    dataModify() {
      this.dataSet = [
        {name: 'a', value: 25},
        {name: 'b', value: 28},
        {name: 'c', value: 12},
        {name: 'd', value: 25},
        {name: 'e', value: 43},
        {name: 'f', value: 55}
      ]
      this.repaintChart()
    },
    drawChart() {
     //* 속성값 DI
     const {width, height, margin, dataSet} = this

     //* x 척도
     const x = d3.scaleBand()
      .domain(dataSet.map(d => d.name))
      .range([margin.left, width - margin.right])
      .padding(0.2);
  
    //* y 척도
    //! Nice:
    const y = d3.scaleLinear()
      .domain([0, d3.max(dataSet, d => d.value)]).nice()
      .range([height - margin.bottom, margin.top]);
  
    //* x축 만들기
    const xAxis = g => g
      .attr('transform', `translate(0, ${height - margin.bottom})`)
      .call(d3.axisBottom(x)
      .tickSizeOuter(0));
  
    //* y축 만들기
    const yAxis = g => g
      .attr('transform', `translate(${margin.left}, 0)`)
      .call(d3.axisLeft(y))
      .call(g => g.select('.domain').remove());
  
    //! svg 진입점
    const svg = d3.select('section').append('svg').style('width', width).style('height', height);
    
    //! 축 붙이기
    svg.append('g').call(xAxis);
    svg.append('g').call(yAxis);

    //! enter로 데이터 바인딩
    svg.append('g')
        .attr('fill', 'green')
        .selectAll('rect')
        .data(dataSet).enter().append('rect')
        .attr('x', d => x(d.name))
        .attr('y', d => y(d.value))
        .attr('height', d => y(0) - y(d.value))
        .attr('width', x.bandwidth());

    svg.node();
    },
    repaintChart() {
      d3.select('section svg').remove()
      this.drawChart()
    }
  }
}
</script>

<style scoped>

</style>