<!-- Your HTML goes here -->
<template>
  <svg class='pc-container' :viewBox='viewBox'>
    <g class='pc-lines'></g>
    <g class='pc-axes'></g>
  </svg>
</template>

<script>
import * as d3 from 'd3'
export default {
  name: 'ParallelCoordinateChart', // Feel free to rename this and the file
  props: {
    dataset: {
      required: true
    },
    attrVal: {
      required: true
    },
    groupVal: {
      required: true
    },
    clicked: {
      required: true
    },
    width: {
      required: true
    },
    height: {
      required: true
    }
  },
  data() {
    return {
      keys: null,
      selected: null,
      groupSelected: false,
      margin: {
        top: 20,
        bottom: 20,
        left: 20,
        right: 20,
      }
    }
  },
  watch: {
    clicked() {
      this.groupSelected = true
      this.update()
      this.groupSelected = false
    }
  },
  mounted() {
    this.init()
  },
  computed: {
    viewBox() {
      return `0 0 ${this.width} ${this.height}`;
    },
  },
  methods: {
    init() {
      console.log(this.selection)
      this.x = null
      this.y = d3.scalePoint()
      this.brush = d3.brushX()
      // Selector. To be implemented at later point.
      // this.z = d3.scaleSequential()
      this.line = d3.line()
      this.update()
    },
    update() {
      // add columns manually
      let columns = ['Critic_Score', 'User_Score', 'Total_Shipped', 'Global_Sales']
      if (this.clicked) {
        this.dataset = d3.filter(d => d[this.groupVal] == this.clicked)
      }
      this.dataset = Object.assign(this.dataset, {columns})
      this.keys = this.dataset.columns
      console.log(this.keys)
      this.selections = new Map()
      console.log(this.selections)
      this.x = new Map(Array.from(this.keys, key => [key, d3.scaleLinear(d3.extent(this.dataset, d => d[key]),
          [this.margin.left, this.width - this.margin.right])]))
      console.log(this.x)
      this.y = d3.scalePoint(this.keys, [this.margin.top, this.height - this.margin.bottom])
      console.log(this.y)
      this.brush = d3.brushX()
          .extent([
            [this.margin.left, -(50 / 2)],
            [this.width - this.margin.right, 50 / 2]
          ])
          .on("start brush end", this.brushed);
      d3.select('.pc_container')
          .call(this.brush)
      // this.z =
      this.line = d3.line()
          .defined(([, value]) => value != null)
          .x(([key, value]) => this.x.get(key)(value))
          .y(([key]) => this.y(key))
      console.log(this.line)
      this.render_lines()
      if (this.groupSelected == false) {
        this.render_axes()
      }
    },
    render_axes() {
      d3.select('.pc-axes')
          .selectAll('g')
          .data(this.keys)
          .join('g')
          .attr("transform", d => `translate(0,${this.y(d)})`)
          .each((d, i, nodes) => {
            // This is where the issue is.
            d3.select(nodes[i]).call(d3.axisBottom(this.x.get(d)));
          })
          .call(g => g.append("text")
              .attr("x", this.margin.left)
              .attr("y", -6)
              .attr("text-anchor", "start")
              .attr("fill", "black")
              .text(d => this.axis_text(d)))
          .call(g => g.selectAll("text")
              .clone(true).lower()
              .attr("fill", "none")
              .attr("stroke-width", 5)
              .attr("stroke-linejoin", "round")
              .attr("stroke", "white"))
          .call(this.brush)
    },
    render_lines() {
      d3.select('.pc-lines')
          .attr('fill', 'none')
          .attr('stroke-width', 1.5)
          .attr('stroke-opacity', 0.4)
          .selectAll('path')
          .data(this.dataset)
          .join("path")
          .attr("stroke", 'steelblue')
          .attr("d", d => this.line(d3.cross(this.keys, [d], (key, d) => [key, d[key]])))
          .append("title")
          .text(d => d.name);
    },
    axis_text(s) {
      console.log(s)
      let labels = {Critic_Score:'Critic Score', User_Score:'User Score', Total_Shipped:'Total Shipped (m)',
        Global_Sales:'Global Sales (m)'}
      return labels[s]
    },
    // brushed function taken from brushable scatterplot located at https://observablehq.com/@d3/brushable-parallel-coordinates
    brushed({selection}, key) {
      if (selection === null) this.selections.delete(key);
      else this.selections.set(key, selection.map(this.x.get(key).invert));
      const selected = [];
      d3.select('.pc-lines')
          .selectAll('path')
          .each((d, i, nodes) => {
            const active = Array.from(this.selections).every(([key, [min, max]]) => d[key] >= min && d[key] <= max);
            d3.select(nodes[i]).style("stroke", active ? 'steelblue' : '#ddd');
            if (active) {
              d3.select(nodes[i]).raise();
              selected.push(d);
            }
          })
          .property("value", selected).dispatch("input");
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* Add your CSS here */
</style>
