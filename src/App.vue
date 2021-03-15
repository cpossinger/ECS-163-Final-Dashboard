<template>
  <v-app v-bind:style="{background: path}">
    <v-app-bar
        app
        color="primary"
        dark
        v-if="component === 'Main'"
    >
      <v-app-bar-title>Video Games 1980-2019</v-app-bar-title>
      <v-spacer></v-spacer>

      <v-select
          v-model="attrVal"
          label="Select Attribute"
          :items="attrValItems"
          item-text="label"
          item-value="value"
          hide-details
          :menu-props="{top: true, offsetY: true}"
      ></v-select>

      <v-btn @click="toggleDarkTheme" text rounded>Toggle Dark Theme</v-btn>


    </v-app-bar>
    <head>
      <link href="https://fonts.googleapis.com/css?family=Press+Start+2P" rel="stylesheet">
      <link href="https://unpkg.com/nes.css/css/nes.css" rel="stylesheet" />
    </head>
    <v-main>
      <v-container fill-height fluid>
        <v-row v-if="component === 'Start_Menu'" align="center" justify="center"  no-gutters >
          <h1 align="center" class="nes-text">Welcome to the VGS Dashboard</h1>

        </v-row>
        <v-row v-if="component === 'Start_Menu'" align="center" justify="center"  no-gutters >
          <h2 align="center" class="nes-text">Click Next for Tutorial</h2>
        </v-row>



        <v-row v-if="['StreamGraph','BarChart','ParallelCoordinateChart','End_Menu'].includes(component)" align="center" justify="center" no-gutters>
          <div class="nes-container with-title is-centered is-dark">
            <p v-if="component === 'StreamGraph'" class="title">Stream graph</p>
            <p v-if="component === 'BarChart'" class="title">Bar Chart</p>
            <p v-if="component === 'ParallelCoordinateChart'" class="title">Parallel Coordinate Chart</p>
            <v-row>
                <v-img src="/data/wizard.gif" max-height="115" max-width="100"></v-img>
              <v-col>
                <p align="left" class="nes-text" v-if="component === 'StreamGraph'"></p>
                <p class="nes-text" v-if="component === 'BarChart'">Bar Chart!</p>
                <p class="nes-text" v-if="component === 'ParallelCoordinateChart'">Parallel Coordinate Chart!</p>
                <p class="nes-text" v-if="component === 'End_Menu'">Done!</p>
              </v-col>

            </v-row>


          </div>
        </v-row>


        <v-row v-if="['StreamGraph'].includes(component)" align="center" justify="center" no-gutters>
        <v-autocomplete
            v-model="autoValStream"
            :items="autoItemsStream"
            :label="autoLabelStream"
            rounded
            filled
            clearable
        >
        </v-autocomplete>
        </v-row>

        <v-row v-if="component === 'ParallelCoordinateChart'" align="center" justify="center" no-gutters>
          <v-range-slider
             :value="yearRangePC"
              max="2019"
              min="1980"
              vertical
              thumb-label="always"
          ></v-range-slider>

        </v-row>


        <v-row id="component-row">
          <keep-alive>
            <transition name="component-fade" mode="out-in">
              <!--
              <component :is="component" v-bind="currentProps" v-if="dataset"  @selectedGroupStream="setClickedStreamVal" />
              -->

              <component :is="component" v-bind="currentProps" v-if="dataset"  />

            </transition>
          </keep-alive>
        </v-row>

        <v-row v-if="component != 'Main'" align="center" justify="center" no-gutters>
          <button id="back" type="button" v-if="['StreamGraph','BarChart','ParallelCoordinateChart','End_Menu'].includes(component)" class="nes-btn" @click="back">Back</button>
          <button id="skip" type="button" v-if="component === 'Start_Menu'" class="nes-btn" @click="skip">Skip</button>
          <button id="next" align="right" type="button" v-if="['Start_Menu','StreamGraph','BarChart','ParallelCoordinateChart'].includes(component)" class="nes-btn" @click="next">Next</button>
          <button id="finish" type="button" v-if="component === 'End_Menu'" class="nes-btn" @click="finish">Finish</button>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Main from './components/Main.vue'
import Start_Menu from './components/Start_Menu.vue'
import End_Menu from "@/components/End_Menu";
import StreamGraph from "@/components/StreamGraph";
import ParallelCoordinateChart from "@/components/ParallelCoordinateChart";
import BarChart from "@/components/BarChart";
import * as d3 from "d3";
export default {
  name: 'App',
  components: {
    Main,
    Start_Menu,
    End_Menu,
    BarChart,
    StreamGraph,
    ParallelCoordinateChart
  },
  data: () => ({
    component: "Start_Menu",
    dataset: [],

    attrValItems: [
      {value: 'Critic_Score', label: "Critic Score"},
      {value: 'User_Score', label: "User Score"},
      {value: 'Total_Shipped', label: "Total Shipped"},
      {value: 'Global_Sales', label: "Global Sales"},
      {value: 'NA_Sales', label: "North America Sales"},
      {value: 'PAL_Sales', label: "PAL Region Sales"},
      {value: 'JP_Sales', label: "Japan Sales"},
    ],
    attrVal: "Global_Sales",
    path: "url(/data/start_menu.png)",
    //selectedGroupStream: "Sports",
    //selectedGroupsBar: {attrib: "Platform", values: []},
    autoValStream: "",
    autoLabelStream: "Select Genre",
    autoItemsStream: [],
    yearRangePC: [1980,2019]

  }),
  watch: {
    dataset: {
      handler: function (){
        this.updateAuto();
      },
    },
    component: {
      handler: function (){
        this.changeDisplay();
      },
    }

  },
  mounted() {
  },
  methods: {
    /*
    barChartFiltering(attribX, selected) {
      console.log("The bar chart said to filter the data on column", attribX, "with values", selected)
      this.selectedGroupsBar = {attrib: attribX, values: selected}
    },
   */



changeDisplay(){
      if(this.component === "StreamGraph" || this.component === "ParallelCoordinateChart" ||this.component === "Start_Menu" || this.component === "End_Menu" || this.component === "BarChart"){
        document.getElementById("component-row").style.display = "block";
      }else{
        document.getElementById("component-row").style.display = "flex";
      }



    },

    updateAuto() {
      let data = this.dataset.filter( obj => obj["Genre"] != 0)
       this.autoItemsStream =   [...new Set(data.map(item => item[this.groupValStream]))].filter(str => str != null);
    },
    toggleDarkTheme() {
      this.$vuetify.theme.themes.dark.anchor = "#41B883"
      this.$vuetify.theme.dark = !this.$vuetify.theme.dark
    },
    next() {
      if(this.component === "Start_Menu"){
        this.component = "StreamGraph";
        this.path = "url(/data/dungeon_room1.jpg)";
      }else if(this.component === "StreamGraph"){
        this.component = "BarChart";
        this.path = "url(/data/dungeon_room2.png)";
      }else if(this.component === "BarChart"){
        this.component = "ParallelCoordinateChart";
        this.path = "url(/data/dungeon_room3.png)";
      } else{
        this.component = "End_Menu";
        this.path = "url(/data/end_menu.jpg)";
      }
    },
    back(){
      if(this.component === "End_Menu"){
        this.component = "ParallelCoordinateChart";
        this.path = "url(/data/dungeon_room3.png)";
      }else if(this.component === "StreamGraph"){
        this.component = "Start_Menu";
        this.path = "url(/data/start_menu.png)"
      }else if(this.component === "BarChart"){
        this.component = "StreamGraph";
        this.path = "url(/data/dungeon_room1.jpg)";
      } else{
        this.component = "BarChart";
        this.path = "url(/data/dungeon_room2.png)";
      }
    },
    skip() {
      this.component = "Main";
      let app = document.getElementById("app")
      app.style.removeProperty("background")

    },

    finish(){
      this.component = "Main";
      let app = document.getElementById("app")
      app.style.removeProperty("background")
    },
    label(s) {
      let labels =
          {Critic_Score: "Critic Score",
            User_Score: "User_Score",
            Total_Shipped: "Total Shipped",
            Global_Sales: "Global_Sales",
            NA_Sales: "North America Sales",
            PAL_Sales: "PAL Region Sales",
            JP_Sales: "Japan Sales"}
      return labels[s]
    },
    /*
    setClickedStreamVal(val){
      console.log('setClicked called')
      this.selectedGroupStream = val;
    },

     */
  },
  created() {
    console.log("created");
    d3.csv('/data/vgsales-12-4-2019.csv', d3.autoType).then((data) => {
      data = data.filter(obj => obj.Year != null);
      data = data.filter(obj => obj.Year >= 1980);
      console.log("data: ", data);
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
          NA_Sales: +d.NA_Sales,
          JP_Sales: +d.JP_Sales,
          PAL_Sales: +d.PAL_Sales,
        })
      })
    });
  },
  computed: {
    // eslint-disable-next-line vue/return-in-computed-property
    currentProps: function(){
      if(this.component === "StreamGraph"){
        console.log(true);
        return {
          dataset: this.dataset,
          width:'500',
          height:'150',
          attrVal:"Total_Shipped",
          groupVal:"Genre",
          setClicked: this.setClickedStreamVal,
          streamAutoVal: this.autoValStream
        }
      }
      else if(this.component === "BarChart"){
        return {
          dataset: this.dataset,
          width:"500",
          height: "200",
          attribX: 'Platform',
          attribY: 'Total_Shipped',
          setFilter: this.barChartFiltering,
          streamGroup: 'Genre',
          selectedGroupStream: 'Sports'
        }
      }
      else if(this.component === "ParallelCoordinateChart"){
        return {
          dataset: this.dataset,
          attrVal: "Total_Shipped",
          groupVal: "Genre",
          selectedGroupStream: 'Sports',
          width: "200",
          height: "200"
        }
      }
      else if(this.component === "Main"){
        console.log("rendermain", this.selectedGroupsBar)
        return {
          dataset: this.dataset,
          attrVal: this.attrVal,
          //selectedGroupStream: this.selectedGroupStream,
          //selectedGroupsBar: this.selectedGroupsBar,
          //barChartFiltering: this.barChartFiltering,
          //setClickedStreamVal: this.setClickedStreamVal,
        }
      }
    },
  }
}
</script>

<style>
.nes-btn {
  height:50px;
  width:200px;
  margin: -20px -50px;
  position:relative;
  top:10%;
}
.v-application {
  font-family: "Press Start 2P";
}
.v-application .title {
  font-family: "Press Start 2P" !important;
}
.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-to
  /* .component-fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

text {
  fill: currentColor;
}
.v-slider--vertical {
  min-height: 750px;
}
.v-slider__thumb-label {
  font-size: 0.45em;
}
</style>