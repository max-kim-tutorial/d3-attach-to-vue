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
      const { width, dataSet, height, margin } = this
      const xScale = d3.scaleBand()
        .domain(d3.range(dataSet.length))
        .rangeRound([0, width])
        .padding(1)
      const yScale = d3.scaleLinear()
        .domain([0, d3.max(dataSet)])
        .rangeRound([height-margin.bottom, margin.top])
        .nice()
      return { xScale, yScale }
    },
    getAxis() {
      const { height } = this
      const { yScale } = this.getScales()
      const axis = g => g
        .attr("y", (d) => height - yScale(d))
        .attr('transform', `translate(30, 0)`)
        .attr('class', 'yAxis')
        .call(d3.axisLeft(yScale))
      return axis
    },
    drawChart() {
      //? 데이터셋 개수가 달라졌을 경우는? => 그럴땐 append가 필요할텐데
      const { dataSet, width, height, margin } = this
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
        .attr("y", (d) => yScale(d))
        .attr("height", d => height - yScale(d) - margin.bottom)
        .attr("width", 40)
        .attr('fill', 'green')

      svg.selectAll("text")
        .data(dataSet)
        .enter()
        .append("text")
        .text(d => d)
        .attr('class', 'label')
        .attr('x', (d,i) => xScale(i) + 20)
        .attr('y', d => yScale(d) - 5)
        .attr('fill', 'black')
        .attr('text-anchor', 'middle')
        .attr('font-size', '12')

      svg.append('g').call(this.getAxis());
      })
      
    },
    updateChart() {
      const { dataSet, height, margin } = this
      const { xScale, yScale } = this.getScales()

      this.$nextTick(() => {
        const svg = d3.select("#bar-chart")
        svg.selectAll('rect')
          .data(dataSet)
          .transition()
          .duration(1000)
          .attr("x", (d,i) => xScale(i))
          .attr("y", (d) => yScale(d))
          .attr("height", d => height - yScale(d) - margin.bottom)
          .attr('fill', 'blue')

        svg.selectAll("text.label")
          .data(dataSet)
          .text(d => d)
          .transition()
          .duration(1000)
          .attr('x', (d,i) => xScale(i) + 20)
          .attr('y', d => yScale(d) - 5)

        svg.select('.yAxis')
          .transition()
          .duration(1000)
          .call(this.getAxis())
      })
    }
  }
}
</script>

<style>

</style>