<!-- Your HTML goes here -->
<template>
     <svg class='container' :viewBox='viewBox'>
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
            margin: {
               top: 20,
               bottom: 20,
               left: 20,
               right: 20,
            }
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
            console.log(this.dataset)

            this.x = null
            this.y = d3.scalePoint()
    
            // Selector. To be implemented at later point.
            // this.z = d3.scaleSequential()

            this.line = d3.line()

            this.update()
        },
        update() {
            
            // add columns manually
            let columns = ['Critic_Score', 'User_Score', 'Total_Shipped', 'Global_Sales']
            this.dataset = Object.assign(this.dataset, {columns})
            this.keys = this.dataset.columns
            console.log(this.keys)

            this.x = new Map(Array.from(this.keys, key => [key, d3.scaleLinear(d3.extent(this.dataset, d => d[key]), 
                [this.margin.left, this.width - this.margin.right])]))
            console.log(this.x)

            this.y = d3.scalePoint(this.keys, [this.margin.top, this.height - this.margin.bottom])
            console.log(this.y)
            
            // this.z = 

            this.line = d3.line()
                .defined(([, value]) => value != null)
                .x(([key, value]) => this.x.get(key)(value))
                .y(([key]) => this.y(key))
            console.log(this.line)

            this.render_lines()
            this.render_axes()
        },
        render_axes() {
            d3.select('.pc-axes')
                .selectAll('g')
                .data(this.keys)
                .join('g')
                    .attr("transform", d => `translate(0,${this.y(d)})`)
                    .each((d, i, nodes) => {
                        d3.select(nodes[i]).call(d3.axisBottom(this.x.get(d)));
                    })
                    .call(g => g.append("text")
                        .attr("x", this.margin.left)
                        .attr("y", -6)
                        .attr("text-anchor", "start")
                        .attr("fill", "black")
                        .text(d => d))
                    .call(g => g.selectAll("text")
                        .clone(true).lower()
                        .attr("fill", "none")
                        .attr("stroke-width", 5)
                        .attr("stroke-linejoin", "round")
                        .attr("stroke", "white"));
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
        // brushed function taken from brushable scatterplot located at https://observablehq.com/@d3/brushable-scatterplot
        // copied from project 4, to be repurposed
        brushed({selection}) {
            let value = [];
            if (selection) {
                const [[x0, y0], [x1, y1]] = selection;
                value = d3.select('.points').selectAll('circle')
                    .style('stroke-width', 1.3)
                    .filter(d => x0 <= this.x(d.stats.attack) && this.x(d.stats.attack) < x1 && y0 <= this.y(d.stats.defense) && this.y(d.stats.defense) < y1)
                    .style('stroke-width', 2.5)
                    .data();
              
            } else {
                d3.select('.points').selectAll('circle')
                    .style('stroke-width', 1.3)
            }
            d3.select('.sp_container')
                .property("value", value).dispatch("input");
            console.log(value)
            this.$emit('selected_pokemon', value)
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* Add your CSS here */
</style>