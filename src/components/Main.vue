<template>
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
              @clicked="setClickedVal"
          />
        </v-col>
      </v-row>
      <v-row align-content-lg="center" no-gutters>
        <v-col lg=true align="center">
          <ParallelCoordinateChart
              class='pc-chart'
              v-if='dataset'
              :dataset='dataset'
              :attrVal="attrVal"
              :groupVal="groupVal"
              :clicked="clicked"
              width='800'
              height='600'

          />
        </v-col>
        <v-col lg=true align="center">
          <BarChart
              class='bar-chart'
              v-if='dataset'
              :dataset='dataset'
              :width='500'
              :height='500'
              :attrVal="attrVal"
              :groupVal="groupVal"
              attribX= 'Genre'
              attribY='Total_Shipped'
              :setFilter="barChartFiltering"
          />
        </v-col>
      </v-row>
  </v-main>

</template>

<script>

import StreamGraph from "@/components/StreamGraph";
import ParallelCoordinateChart from "@/components/ParallelCoordinateChart";
import BarChart from "@/components/BarChart";

export default {
  name: 'Main',
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
    barChartFiltering: {
      required: true
    },
    clicked: {
      required: true
    }
  },
    watch: {
      attrVal: {
        handler: function (val){
          this.attrVal = val;
        },
        deep: true
      },
      clicked: {
        handler: function (val){
          this.clicked = val;
        },
        deep: true
      },
      groupVal: {
        handler: function (val){
          this.groupVal = val;
        },
        deep: true
      }
    },

  components: {
    StreamGraph,
    ParallelCoordinateChart,
    BarChart
  },

  data: () => ({
    dataset: null,
    selectedGroup: null

  }),

  methods: {
    setClickedVal(val){
      this.selectedGroup = val;
    }
  }
};
</script>

<style>
.stream-graph {
  padding-top: 40px;
  padding-left: 80px;
  padding-right: 80px;
  border-color: black;
}
</style>
