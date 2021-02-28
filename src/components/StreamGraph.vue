<!-- Your HTML goes here -->
<template>
    <div>Stream Graph</div>
</template>

<script>
import * as d3 from 'd3'

export default {
    name: 'StreamGraph', // Feel free to rename this and the file
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
    data: () =>  ({

    }),
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
            console.log(d3)
          console.log(this.dataset)
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
/* Add your CSS here */
</style>