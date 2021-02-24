<!-- This file will be used to layout and connect your views -->
<template>
    <div id="app">
        <!-- This is only a basic setup to display your views. Use HTML and CSS to create your layout using these basic commands -->
        <StreamGraph 
            class='stream-graph'
            v-if='dataset'
            :dataset='dataset'
            width='0'
            height='0'
        />
        <ParallelCoordinateChart 
            class='pc-chart'
            v-if='dataset'
            :dataset='dataset'
            width='0'
            height='0'
        />
        <BarChart 
            class='bar-chart'
            v-if='dataset'
            :dataset='dataset'
            width='0'
            height='0'
        />
    </div>
</template>

<script>
// You are free to define more than three views!
// Try to use descriptive names like 'OverviewScatterPlot' or 'DetailLineChart'
import StreamGraph from './components/StreamGraph.vue'
import ParallelCoordinateChart from './components/ParallelCoordinateChart.vue'
import BarChart from './components/BarChart.vue'

export default {
    name: 'App',
    components: {
        StreamGraph,
        ParallelCoordinateChart,
        BarChart
    },
    data() {
        return {
            dataset: null
        }
    },
    created() {

    },
    methods: {

    }
}
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
