<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <v-app-bar-title>Video Games 1980-2019</v-app-bar-title>
      <v-spacer></v-spacer>
      <v-select
          v-model="groupVal"
          label="Select Grouping Attribute"
          :items="groupValItems"
          hide-details
          :menu-props="{top: true, offsetY: true,}"
      ></v-select>
      <v-select
          v-model="attrVal"
          label="Select Attribute"
          :items="attrValItems"
          hide-details
          :menu-props="{top: true, offsetY: true}"
      ></v-select>
      <v-btn @click="toggleDarkTheme" text rounded>Toggle Dark Theme</v-btn>
    </v-app-bar>

    <v-main>
      <v-row align-content-lg="center" no-gutters>
        <v-col lg=true>
      <StreamGraph
          class='stream-graph'
          v-if='dataset'
          :dataset='dataset'
          :width='500'
          :height='200'
          :attrVal="attrVal"
          :groupVal="groupVal"
      />
        </v-col>
      </v-row>
      <v-row align-content-lg="center" no-gutters>
        <v-col lg=true align="center">
      <ParallelCoordinateChart
          class='pc-chart'
          v-if='dataset'
          :dataset='dataset'
          width='0'
          height='0'
      />
        </v-col>
        <v-col lg=true align="center">
      <BarChart
          class='bar-chart'
          v-if='dataset'
          :dataset='dataset'
          width='0'
          height='0'
      />
        </v-col>
      </v-row>


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

  data: () => ({
    dataset: null,
    groupValItems: ["Genre","Platform","Publisher","Developer"],
    attrValItems: ["Critic_Score","User_Score","Total_Shipped","Global_Sales","NA_Sales","PAL_Sales","JP_Sales"],
    groupVal: "Genre",
    attrVal: "Global_Sales"

  }),
  created() {
    console.log("created");
     d3.csv('/data/vgsales-12-4-2019.csv', d3.autoType).then((data) => {
       data = data.filter(obj => obj.Year != undefined);
       data = data.filter(obj => obj.Year >= 1980);
       this.dataset = d3.map(data, (d) => {
         return Object.assign(d, {
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
           Year:  new Date(Date.UTC(d.Year)),
           //might have spelled something wrong
           Vgchartzscore: +d.Vgchartzscore,
           NA_Sales: +d.NA_Sales,
           JP_Sales: +d.JP_Sales,
           PAL_Sales: +d.PAL_Sales,
         })
       })
     });

  },
  methods: {
    toggleDarkTheme() {
      this.$vuetify.theme.themes.dark.anchor = "#41B883"
      this.$vuetify.theme.dark = !this.$vuetify.theme.dark
    }
  }
};
</script>
