<!-- Your HTML goes here -->
<template>
  <div>
    <svg class="bar-container" :width="width + 50" :height="height + 50" id="barchart" :viewBox='viewBox' >
      <g transform="translate(100, 0)" class='bar-vertical'></g>

      <g transform="translate(100, 500)" class='bar-horizontal'></g>
      <g class="bars"></g>
    </svg>
  </div>
</template>

<script>
import * as d3 from 'd3'
const buffer = 100
export default {
  name: 'BarChart', // Feel free to rename this and the file
  props: {
    dataset: {
      required: true
    },
    width: {
      required: true
    },
    height: {
      required: true
    },
    attribX: {
      type: String,
      required: true
    },
    attribY: {
      type: String,
      required: true
    },
    selectedGroupStream: {
     type: String,
     required: true
    },
    setFilter: {
      type: Function,
      required: true
    }
  },
  data() {
    return {
      vertical: null,
      horizontal: null,
      selection: [],
      fixed_data: []
    }
  },
  mounted() {
    this.init()
  },
  computed: {
    viewBox() {
      return `0 0 ${this.width +1.25* buffer} ${this.height+buffer}`;
    },
  },
  methods: {
    renderData() {
      this.total = 0
      this.fixed_data = []
      d3.group(this.dataset, d=> {
        return d[this.attribX]
      })
          .forEach(
              // eslint-disable-next-line no-unused-vars
              (value, key) => {
                let sum = 0;
                for (let i = 0; i < value.length; i++) {
                  sum += value[i][this.attribY]
                }
                this.fixed_data.push({bar: value[0][this.attribX], data:sum})
                this.total += sum
              }
          )
      console.log("fixed data: ",this.fixed_data);
      this.horizontal = d3.scaleLinear().domain([d3.min(this.fixed_data, d=>d.data), 1.1* d3.max(this.fixed_data, d=>d.data)]).range([0, this.width])
      this.vertical = d3.scaleBand(this.fixed_data.map(d => d.bar), [0, this.height])
      this.renderBars()
      /*
      bars.append("text")
          .attr("class", "label")
          .attr("y", d => this.vertical(d.bar)+this.vertical.bandwidth()/2+4)
          .attr("x", d => this.horizontal(d.data) + 4 + buffer)
          .text(d => d.data.toFixed(0))
  */
    },
    renderBars() {
      d3.select(".bars").selectAll("*").remove()
      let bars = d3.select(".bars")
      for (let i = 0; i < this.fixed_data.length; i++) {
        let d = this.fixed_data[i]
        let sel = this.selection.length == 0 || this.selection.includes(d.bar)
        bars.append("rect")
            .attr("class", "bar")
            .attr("y", this.vertical(d.bar) + 0.1 * this.vertical.bandwidth())
            .attr("height", 0.8 * this.vertical.bandwidth())
            .attr("x", buffer)
            .attr("width", this.horizontal(d.data))
            .attr("data", d.bar)
            .on("click", d => {
              let clicked = d3.select(d.target).attr("data")
              let index = this.selection.indexOf(clicked)
              if (index != -1) {
                this.selection.splice(index, 1)
              } else {
                this.selection.push(clicked)
              }
              this.renderBars()
              this.setFilter(this.attribX, this.selection)
            })
            .attr("fill", "#CCC")
            .filter(() => sel)
            .attr("fill", "#377db6")
        bars.append("text")
            .attr("class", "label")
            .attr("y", this.vertical(d.bar) + this.vertical.bandwidth()/2 + 4)
            .attr("x", this.horizontal(d.data) + 4 + buffer)
            .text(d.data.toFixed(2))
      }
    },
    renderAxes() {
      let vert = d3.axisLeft(this.vertical)
      d3.select(".bar-vertical").call(vert)
      let hori = d3.axisBottom(this.horizontal)
      d3.select(".bar-horizontal").call(hori)
    },
    init() {
      d3.select(".barchart").attr("width", this.width).attr("height", this.height)
      this.renderData()
      this.renderAxes()
    }
  },
  watch: {
    data() {
      // Chart data was updated by main
      // Must check that every column in selection is still in the dataset, or remove it otherwise.
      for (let i = 0; i < this.selection.length; i++) {
        if (this.data.map(d => d[this.attribX]).includes(this.selection[i])){
          this.selection.splice(i, 1)
          i--
        }
      }
      this.init()
    },
    attribX() {
      this.selection = []
      this.init()
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
/* Add your CSS here */
</style>
