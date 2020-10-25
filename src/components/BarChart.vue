<template>
  <div class="bar-chart">
    <section id="bar-chart" />
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
    width:{
      type:Number,
      default:400
    },
    height: {
      type:Number,
      default:300
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
      handler() {
        this.updateChart()
      }
    }
  },
  created() {
    this.drawChart()
  },
  methods: {
    getScales() {
      const { width, dataSet } = this

      const xScale = d3.scaleBand()
        .domain(d3.range(dataSet.length))
        .rangeRound([0, width])
        .padding(0)
      const yScale = d3.scaleLinear()
        .domain([0, d3.max(dataSet)])
        .rangeRound([0, 300])
      return { xScale, yScale }
    },
    drawChart() {
      const { dataSet, width, height } = this
      const { xScale, yScale } = this.getScales()

      this.$nextTick(() => {
      const svg = d3.select("#bar-chart").append('svg')
        .style('width', width)
        .style('height', height);

      svg.selectAll('rect')
        .data(dataSet)
        .enter()
        .append('rect')
        .attr("x", (d,i) => xScale(i))
        .attr("y", (d) => height - (d*2))
        .attr("height", d => yScale(d))
        .attr("width", 40)
        .attr('fill', 'green')

      svg.selectAll("text")
        .data(dataSet)
        .enter()
        .append("text")
        .text(d => d)
        .attr('x', (d,i) => xScale(i) + 20)
        .attr('y', d => height - (d*2) - 10)
        .attr('fill', 'black')
        .attr('text-anchor', 'middle')
        .attr('font-size', '12')
      })
    },
    updateChart() {
      const { dataSet, height } = this
      const { xScale, yScale } = this.getScales()

      this.$nextTick(() => {
        const svg = d3.select("#bar-chart")
        svg.selectAll('rect')
          .data(dataSet)
          .attr("x", (d,i) => xScale(i))
          .attr("y", (d) => height - (d*2))
          .attr("height", d => yScale(d))
          .attr('fill', 'blue')

        svg.selectAll("text")
          .data(dataSet)
          .text(d => d)
          .attr('x', (d,i) => xScale(i) + 20)
          .attr('y', d => height - (d*2) - 10)
      })
    }
  }
}
</script>

<style>

</style>