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
              :groupVal="groupValStream"
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
              :groupVal="groupValStream"
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
              :attribX= 'attrVal'
              :attribY='groupValBar'
              :selectedGroupStream="selectedGroupStream"
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
    groupValBar: {
      required: true
    },
    groupValStream: {
      required: true
    },
    selectedGroupStream: {
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
      groupValBar: {
        handler: function (val){
          this.groupValBar = val;
        },
        deep: true
      },
      clicked: {
        handler: function (val){
          this.clicked = val;
        },
        deep: true
      },
      groupValStream: {
        handler: function (val){
          this.groupValStream = val;
        },
        deep: true
      },
      selectedGroupStream: {
        handler: function (val){
          this.selectedGroupStream = val;
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
    selectedGroup: null

  }),

  methods: {

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
