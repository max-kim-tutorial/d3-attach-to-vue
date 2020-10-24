<template>
  <div>
    <button @click="drawChart">chart</button>
    <section class='line-chart' />
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name:'LineChart',
  props: {
    dataSet: {
      type: Array,
      default:() => [],
    },
    width:{
      type:Number,
      default:500
    },
    height: {
      type:Number,
      default:500
    },
    margin:{
      type:Object,
      default: () => ({
        top: 40,
        right: 40,
        bottom: 40,
        left: 40
      })
    }
  },
  watch:{
    dataSet: {
      deep:true,
      handler() {
        this.drawChart()
      }
    }
  },
  methods: {
    getXaxisScale() {
      const { width, margin, dataSet } = this

      const getXScale = d3.scaleBand()
        .domain(dataSet.map(d => d.name))
        .range([margin.left, width - margin.right])
        .padding(0.2);
      return getXScale
    },
    getYaxisScale() {
      const { height, margin, dataSet } = this

      const getYScale = d3.scaleLinear()
      .domain([0, d3.max(dataSet, d => d.value)]).nice()
      .range([height - margin.bottom, margin.top]);
      return getYScale
    },
    getXaxis() {
       const { height, margin } = this
       const x = this.getXaxisScale()

       const xAxis = g => g
        .attr('transform', `translate(0, ${height - margin.bottom})`)
        .call(d3.axisBottom(x).tickSizeOuter(0));
      return xAxis
    },
    getYaxis() {
       const { margin } = this
       const y = this.getYaxisScale()

       const yAxis = g => g
        .attr('transform', `translate(${margin.left}, 0)`)
        .call(d3.axisLeft(y))
        .call(g => g.select('.domain').remove())
      return yAxis
    },
    drawChart() {
     //* 속성값 DI
     const { width,height, dataSet } = this
     const x = this.getXaxisScale()
     const y = this.getYaxisScale()
  
    //! svg 진입점
    const svg = d3.select('section')
      .append('svg')
      .style('width', width)
      .style('height', height);

    const rects = svg.append('g')
      .attr('fill', 'green')
      .selectAll('rect')
      .data(dataSet)
    
    //! 축 붙이기
    svg.append('g').call(this.getXaxis());
    svg.append('g').call(this.getYaxis());

    //! enter
    rects.enter().append('rect')
        .attr('x', d => x(d.name))
        .attr('y', d => y(d.value))
        .attr('height', d => y(0) - y(d.value))
        .attr('width', x.bandwidth())

    rects
      .attr('x', d => x(d.name))
      .attr('y', d => y(d.value))
      .attr('height', d => y(0) - y(d.value))
      .attr('width', x.bandwidth())
    },
    // watch로 연결하거나 하면 좋을듯
    repaintChart() {
      console.log(this.dataSet)
      d3.select('section svg').remove()
      this.drawChart()
    }
  }
}
</script>

<style scoped>
  section {
    transition: 2s;
  }
</style>