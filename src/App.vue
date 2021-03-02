<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >

    </v-app-bar>

    <v-main>
      <StreamGraph
          class='stream-graph'
          v-if='dataset'
          :dataset='dataset'
          width='500'
          height='500'
      />
      <ParallelCoordinateChart
          class='pc-chart'
          v-if='dataset'
          :dataset='dataset'
          width='800'
          height='600'
      />
      <BarChart
          class='bar-chart'
          v-if='dataset'
          :dataset='dataset'
          width='0'
          height='0'
      />

    </v-main>
  </v-app>
</template>

<script>

import StreamGraph from './components/StreamGraph.vue'
import ParallelCoordinateChart from './components/ParallelCoordinateChart.vue'
import BarChart from './components/BarChart.vue'
import * as d3 from 'd3'

export default {
  name: 'App',

  components: {
    StreamGraph,
    ParallelCoordinateChart,
    BarChart
  },

  data() {
    return {
        dataset: null,
    }
  },
  created() {
    console.log("created");
    d3.csv('/data/vgsales-12-4-2019-short.csv', d3.autoType).then((data) => {
        this.dataset = d3.map(data, (d) => ({
                Name: d.Name,
                Genre: d.Genre,
                ESRB_Rating: d.ESRB_Rating,
                Platform: d.Platform,
                Publisher: d.Publisher,
                Developer: d.Developer,
                Critic_Score: +d.Critic_Score,
                User_Score: +d.User_Score,
                Total_Shipped: +d.Total_Shipped,
                Global_Sales: +d.Global_Sales,
                //figure out how to convert number to UTC year instead of local time
                Year: new Date(Date.UTC(d.Year,0,1,0,0,0,0)),
                // might have spelled something wrong -> NaN because not included in short csv, is included in long csv
                // Vgchartzscore: +d.Vgchartzscore
            }))
        })
    },
  methods: {
  }
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 0px;
    display: grid;
    grid-template-rows: 0 0 0 0;
    grid-template-columns: 0 0 0 0;
    grid-template-areas: 
        ". . . ." 
        ". . . ."
        ". . . ."
        ". . . .";
}

.pc-chart {
    width: 800px;
    height: 600px;
}
</style>
