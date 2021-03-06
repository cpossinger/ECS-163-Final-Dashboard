<!-- Your HTML goes here -->
<template>

    <div class="svg-container">
      <v-btn text rounded @click="reset"> Reset Zoom</v-btn>
    </div>
</template>

<script>

import * as d3 from "d3";

export default {
    name: 'StreamGraph',
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
      attrVal: {
          required: true
      },
      groupVal: {
        required: true
      }
    },
  watch: {
      attrVal: {
        handler: function (val){
          this.attrVal = val;
          this.init();
       },
        deep: true
      },
    groupVal: {
      handler: function (val){
        this.groupVal = val;
        this.init();
      },
      deep: true
    }
  },
    data: () =>  ({
      zoom: null,
      svg: null,
      gx: null,
      xAxis: null,
      area: null,
      x: null,
      areas: null,
      Tooltip: null


    }),
  created() {



  },
    mounted() {
        this.init();
    },

    methods: {
        init() {
          let genre_sales = null;
          if(this.attrVal.includes("Score") === true){

            genre_sales = d3.rollup(this.dataset, v => d3.mean(v, d => d[this.attrVal]), d => d.Year, d => d[this.groupVal]);
          }else{

             genre_sales = d3.rollup(this.dataset, v => d3.sum(v, d => d[this.attrVal]), d => d.Year, d => d[this.groupVal]);
          }
          let series_data = [];
           this.series_keys = [];
          for(const map of genre_sales.values()){
            for(const key of map.keys()){
              if(this.series_keys.includes(key) === false){
                this.series_keys.push(key);
              }
            }
          }

          for(const [key,value] of genre_sales){
            let push_obj = ({date: key});
            for(const cat of this.series_keys){
              if(value.get(cat) === undefined){
                push_obj[cat] = 0;
              }else{
               push_obj[cat] = value.get(cat);
              }

            }
            series_data.push(push_obj);

          }
          series_data.sort(function (a,b){
            return a.date - b.date;
          });


          const series_const = d3.stack()
              .keys(this.series_keys)
              .offset(d3.stackOffsetWiggle)
              .order(d3.stackOrderInsideOut)

          this.series = series_const(series_data);
          this.constrStream();
        },

      constrStream(){
        d3.select(".streamGraph").remove();
        let margin = ({top: 0, right: 20, bottom: 30, left: 20});

          this.x =  d3.scaleUtc()
             .domain(d3.extent(this.dataset, d => d.Year))
             .range([margin.left, this.width - margin.right]);
        let y = d3.scaleLinear()
            .domain([d3.min(this.series, d => d3.min(d, d => d[0])), d3.max(this.series, d => d3.max(d, d => d[1]))])
            .range([this.height - margin.bottom, margin.top]);
        let color = d3.scaleOrdinal()
            .domain(Object.keys(this.dataset[0]))
            .range(d3.schemeCategory10);
         this.xAxis = (g,x) => g
            .attr("transform", `translate(0,${this.height - margin.bottom})`)
            .call(d3.axisBottom(x).ticks(this.width / 80).tickSizeOuter(0))
            .call(g => g.select(".domain").remove())

        this.area = (data,x) => d3.area()
            .x(d => x(d.data.date))
            .y0(d =>  y(d[0]))
            .y1(d => y(d[1]))


         this.svg = d3.select(".svg-container")
            .append("svg")
            .attr("class","streamGraph")
            .style("overflow","visible")
            .attr("viewBox", [0, 0, this.width, this.height]);

        this.svg
            .append("defs")
            .append("clipPath")
            .attr("id","clip")
            .append("rect")
            .attr("width",this.width)
            .attr("height",this.height);


         this.Tooltip = this.svg
            .append("text")
            .attr("x", 0)
            .attr("y", 20)
            .style("opacity", 0)
            .style("font-size", 17)




        this.areas = this.svg.append("g")
            .selectAll("path")
            .data(this.series)
      .join("path")
            .on("mouseover",this.over)
            .on("mousemove",this.moved)
            .on("mouseleave",this.leave)
            .on("click",this.clicked)
            .attr("fill", ({key}) => color(key))
            .attr("class","area")
            .attr("clip-path","url(#clip)")
            .attr("d", this.area(this.series,this.x));





         this.zoom = d3.zoom()
            .scaleExtent([1, 32])
            .extent([[margin.left, 0], [this.width - margin.right, this.height]])
            .translateExtent([[margin.left, -Infinity], [this.width - margin.right, Infinity]])
            .on("zoom", this.zoomed);

        this.svg.call(this.zoom)

         this.gx = this.svg.append("g")
            .call(this.xAxis, this.x);
        //let keys = this.series_keys;





      },
      moved(event,d){
          this.Tooltip.text(d.key);
      },
      over(event){
        this.Tooltip.style("opacity", 1);
        d3.selectAll(".area").style("opacity", .2)
            d3.select(event.currentTarget)
            .style("stroke", "black")
            .style("opacity", 1);

      },
      leave(){
        this.Tooltip.style("opacity", 0)
        d3.selectAll(".area").style("opacity", 1).style("stroke", "none")
      },
      clicked(event,d){
      this.$emit("clicked",d.key);
      },

      zoomed(event) {
          const xz = event.transform.rescaleX(this.x);
          this.svg.selectAll("path")
              .attr("d", this.area(this.series,xz))
        this.gx.call(this.xAxis, xz);
},
      reset(){
        this.svg.transition()
            .duration(750)
            .call(this.zoom.transform, d3.zoomIdentity);
      }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
/* Add your CSS here */
</style>