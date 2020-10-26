<template>
  <div class="bar-chart">
    <svg id="bar-chart">
      <g class="bar-chart__bars"></g>
      <g class="bar-chart__axis"></g>
      <g class="bar-chart__mark"></g>
      <g class="bar-chart__label"></g>
    </svg>
    <div class="bar-chart__legend"></div>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name:'BarChart',
  props: {
    dataSet: {
      type: Array,
      default:() => [],
    },
    dataColor: {
      type: Object,
      default: () => ({})
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
  watch: {
    dataSet :{
      deep: true,
      handler(next,prev) {
        console.log(prev, next)
        if (prev.length === next.length) {
          this.updateChart()
        } else if (prev.length < next.length) {
          this.enterChart()
          this.updateChart()
        } else if (prev.length > next.length) {
          this.exitChart()
        }
      }
    }
  },
  created() {
    this.enterChart()
  },
  methods: {
    getScales() {
      const { width, dataSet, height, margin } = this
      const xScale = d3.scaleBand()
        .domain(d3.range(dataSet.length))
        .rangeRound([margin.left, width-margin.left])
      const yScale = d3.scaleLinear()
        .domain([0, d3.max(dataSet.map(d => d.value))])
        .rangeRound([height-margin.bottom, margin.top])
        .nice()
      return { xScale, yScale }
    },
    enterChart() {
      const { dataSet, width, height, margin } = this
      const { xScale, yScale } = this.getScales()
      const graphWidth = (width / dataSet.length) * 0.8

      this.$nextTick(() => {
      const svg = d3.select("#bar-chart")
        .style('width', width)
        .style('height', height);

      svg.select('.bar-chart__bars')
        .selectAll('rect')
        .data(dataSet)
        .enter()
        .append('rect')
        .attr("x", (d,i) => xScale(i))
        .attr("y", (d) => yScale(d.value))
        .attr("height", d => height - yScale(d.value) - margin.bottom)
        .attr("width", graphWidth)
        .attr('fill', 'green')

      svg.select('.bar-chart__mark')
        .selectAll("text")
        .data(dataSet)
        .enter()
        .append("text")
        .text(d => d.value)
        .attr('class', 'mark')
        .attr('x', (d,i) => xScale(i) + (graphWidth/2))
        .attr('y', d => yScale(d.value) - 5)
        .attr('fill', 'black')
        .attr('text-anchor', 'middle')
        .attr('font-size', '12')

      svg.select('.bar-chart__axis')
        .data(dataSet)
        .attr("y", (d) => height - yScale(d.value))
        .attr('transform', `translate(30, 0)`)
        .attr('class', 'axis')
        .call(d3.axisLeft(yScale))

      svg.select('.bar-chart__label')
        .selectAll("text")
        .data(dataSet)
        .enter()
        .append("text")
        .text(d => d.label)
        .attr('class', 'label')
        .attr('x', (d,i) => xScale(i) + (graphWidth/2))
        .attr('y', height - 10)
        .attr('fill', 'black')
        .attr('text-anchor', 'middle')
        .attr('font-size', '12')
      })  
    },
    updateChart() {
      const { dataSet, height, margin, width } = this
      const { xScale, yScale } = this.getScales()
      const graphWidth = (width / dataSet.length) * 0.8

      this.$nextTick(() => {
        const svg = d3.select("#bar-chart")

        svg.selectAll('rect')
          .data(dataSet)
          .transition()
          .duration(500)
          .attr("x", (d,i) => xScale(i))
          .attr("y", (d) => yScale(d.value))
          .attr("height", d => height - yScale(d.value) - margin.bottom)
          .attr("width", graphWidth)
          .attr('fill', 'blue')

        svg.selectAll("text.mark")
          .data(dataSet)
          .text(d => d.value)
          .transition()
          .duration(500)
          .attr('x', (d,i) => xScale(i) + (graphWidth/2))
          .attr('y', d => yScale(d.value) - 5)

        svg.selectAll("text.label")
          .data(dataSet)
          .text(d => d.label)
          .transition()
          .duration(500)
          .attr('x', (d,i) => xScale(i) + (graphWidth/2))
          .attr('y', height - 10)

        svg.select('.axis')
          .transition()
          .duration(1000)
          .call(d3.axisLeft(yScale))
      })
    },
    exitChart() {

    }
  }
}
</script>

<style>

</style>