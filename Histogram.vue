<template>
	<svg id="chart"></svg>
</template>

<script>
import * as d3 from "d3"

export default {
	props: ['data'],
	data() {
		return {
			barsWidth: 500,
			barsHeight: 400,
			axisMargin: 100
		}
	},
	mounted() {
		this.generateHistogram()
	},
	computed: {
		chartHeight() {
			return this.barsHeight + this.axisMargin
		},
		chartWidth() {
			return this.barsWidth + this.axisMargin
		},
		parsedData() {
			let parsedData = []
			Object.keys(this.data).forEach(key => {
				parsedData.push({
					name: this.upperCaseFirstLetter(key),
					value: this.data[key]
				})
			})
			return parsedData
		}
	},
	methods: {
		upperCaseFirstLetter(str) {
			return str.charAt(0).toUpperCase() + str.slice(1)
		},
		generateHistogram() {
			let margin = {top: 10, right: 30, bottom: 30, left: 40}
			let chart = d3.select("#chart")
				.attr("width", this.chartWidth + margin.left + margin.right)
				.attr("height", this.chartHeight + margin.top + margin.bottom)
				.append("g")
					.attr("transform", "translate(" + margin.left + "," + margin.top + ")")

			let tooltip = d3.select("body").append("div")
				.attr("class", "tooltip")

			let yScale = d3.scaleLinear()
				.domain([0, d3.max(this.parsedData, d => {return d.value})])
				.rangeRound([this.barsHeight, 0])

			let xScale = d3.scaleBand()
				.domain(
					this.parsedData.map(
						d => {
							return d.name
						}
					)
				)
				.rangeRound([0, this.barsWidth])
				.padding(0.1)

			let bars = chart.append('g')
				.attr('id', "bars-container")

			bars.selectAll('rect')
				.data(this.parsedData)
				.enter().append("rect")
				.attr("fill", "#6baed6")
				.attr('class', "bar")
				.attr('x', d => { return xScale(d.name) })
				.attr('y', d => { return yScale(d.value) })
				.attr('width', xScale.bandwidth())
				.attr('height', d => { return this.barsHeight-yScale(d.value) })
				.on("mousemove", (d, info) => {
					tooltip.style("display", null)
					tooltip.select("div").html(`${info.name}: <strong>${info.value}</strong>`)
						.style("position", "fixed")
						.style("left", (d.clientX - 30) + "px")
						.style("top", (d.clientY - 20) + "px")
				})
				.on("mouseout", () => {
					tooltip.style("display", "none")
				})

			bars.attr('transform', 'translate('+this.axisMargin+',0)')

			chart.append('g')
				.attr('id','y-axis')
				.call(d3.axisLeft(yScale).ticks(10))
				.attr('transform', 'translate('+this.axisMargin+',0)')

			chart.append('g')
				.attr('id', 'x-axis')
				.call(d3.axisBottom(xScale))
				.attr('transform', 'translate('+this.axisMargin+','+this.barsHeight+')')

			tooltip.append("div")
				.attr("x", 15)
				.attr("dy", "1.2em")
				.style("text-anchor", "middle")
				.style("color", "#000")
				.style("background-color", "#d3d3d3")
				.style("border-radius", "4px")
		}
	}
}
</script>

<style scoped>
html, body {
	font-family: sans-serif;
}
</style>