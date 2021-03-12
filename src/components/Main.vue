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
              :setClicked='setClickedStreamVal'
              :selectedGroupsBar="selectedGroupsBar"
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
              :selectedGroupStream='selectedGroupStream'
              :selectedGroupsBar='selectedGroupsBar'
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
              :attribY= 'attrVal'
              :attribX='groupValBar'
              :streamGroup='groupValStream'
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
    setClickedStreamVal: {
        required: true
    },
    selectedGroupsBar: {
      type: Object
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
          console.log("groupValBar", val)
          this.groupValBar = val;
        },
        deep: true
      },
      groupValStream: {
        handler: function (val){
          console.log("groupValStream", val)
          this.groupValStream = val;
        },
        deep: true
      },
      selectedGroupStream: {
        handler: function (val){
          console.log("selectedGroupStream", val)
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
  mounted() {
    console.log("main mounted", this.selectedGroupsStream)
  },
  methods: {
    init() {
      console.log("main.vue selectedgroupsstream", this.selectedGroupsStream)
    },
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
