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
          v-model="groupValStream"
          label="Select Steam Graph Grouping Attribute"
          :items="groupValItems"
          hide-details
          :menu-props="{top: true, offsetY: true,}"
      ></v-select>
      <v-select
          v-model="attrVal"
          label="Select Attribute"
          :items="attrValItems"
          item-text="label"
          item-value="value"
          hide-details
          :menu-props="{top: true, offsetY: true}"
      ></v-select>
      <v-select
          v-model="groupValBar"
          label="Select Bar Chart Group"
          :items="groupValItems"
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
          <h1 align="center" class="nes-text">Welcome to the VGS Dashboard Tutorial</h1>
        </v-row>


      <v-row v-if="['StreamGraph','BarChart','ParallelCoordinateChart','End_Menu'].includes(component)" align="center" justify="center" no-gutters>
      <div class="nes-container with-title is-centered is-dark">
        <p v-if="component === 'StreamGraph'" class="title">Stream graph</p>
        <p v-if="component === 'BarChart'" class="title">Bar Chart</p>
        <p v-if="component === 'ParallelCoordinateChart'" class="title">Parallel Coordinate Chart</p>
        <v-img src="/data/wizard.gif" max-height="115" max-width="100"></v-img>
        <p class="nes-text" v-if="component === 'StreamGraph'">Stream Graph!</p>
        <p class="nes-text" v-if="component === 'BarChart'">Bar Chart!</p>
        <p class="nes-text" v-if="component === 'ParallelCoordinateChart'">Parallel Coordinate Chart!</p>
        <p class="nes-text" v-if="component === 'End_Menu'">Done!</p>
      </div>
      </v-row>

          <v-row  no-gutters>
      <keep-alive>
        <transition name="component-fade" mode="out-in">
<component :is="component" v-bind="currentProps" v-if="dataset" @clicked="setClickedStreamVal" />

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
    dataset: null,
    groupValItems: ["Genre","Platform","Publisher","Developer"],
    attrValItems: [
      {value: 'Critic_Score', label: "Critic Score"},
      {value: 'User_Score', label: "User Score"},
      {value: 'Total_Shipped', label: "Total Shipped"},
      {value: 'Global_Sales', label: "Global Sales"},
      {value: 'NA_Sales', label: "North America Sales"},
      {value: 'PAL_Sales', label: "PAL Region Sales"},
      {value: 'JP_Sales', label: "Japan Sales"},
    ],
    groupValStream: "Genre",
    groupValBar: "Platform",
    attrVal: "Global_Sales",
    path: "url(/data/start_menu.png)",
    selectedGroupStream: "Sports",
    selectedGroupsBar: {attrib: "Platform", values: []}


  }),
  mounted() {
//    this.toggleDarkTheme();
  },
  methods: {

    barChartFiltering(attribX, selected) {
      console.log("The bar chart said to filter the data on column", attribX, "with values", selected)
      this.selectedGroupsBar = {attrib: attribX, values: selected}
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
      let row = document.getElementsByClassName("row");
      row.style.removeProperty("display")
    },
    finish(){
      this.component = "Main";
      let app = document.getElementById("app")
      app.style.removeProperty("background")
      let row = document.getElementsByClassName("row");
      row.style.removeProperty("display")
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
    setClickedStreamVal(val){
      this.selectedGroupStream = val;
    },

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
      attrVal:"Global_Sales",
      groupVal:"Genre"
       }
      }
      else if(this.component === "BarChart"){

        return {
          dataset: this.dataset,
          width:700,
          height: 700,
          attribX: 'Genre',
          attribY: 'Total_Shipped',
          setFilter: this.barChartFiltering,
          streamGroup: '',
          selectedGroupStream: '' 
        }
      }
      else if(this.component === "ParallelCoordinateChart"){

        return {
          dataset: this.dataset,
          width: "500",
          height: "200"
        }
      }
      else if(this.component === "Main"){
        console.log("rendermain", this.selectedGroupsBar)
        return {
          dataset: this.dataset,
          attrVal: this.attrVal,
          groupValBar: this.groupValBar,
          groupValStream: this.groupValStream,
          selectedGroupStream: this.selectedGroupStream,
          selectedGroupsBar: this.selectedGroupsBar,
          barChartFiltering: this.barChartFiltering
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
  left:40%;
}
.v-application {
  font-family: "Press Start 2P";
}
.v-application .title {
  font-family: "Press Start 2P" !important;
}
.row {
  display: block;
}

.component-fade-enter-active, .component-fade-leave-active {
  transition: opacity .3s ease;
}
.component-fade-enter, .component-fade-leave-to
  /* .component-fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

.nes-text {
  overflow: hidden; /* Ensures the content is not revealed until the animation */
  white-space: nowrap; /* Keeps the content on a single line */
  margin: 0 auto; /* Gives that scrolling effect as the typing happens */
  letter-spacing: .15em; /* Adjust as needed */
  animation:
      typing 3.5s steps(40, end)
}


/* text animation */

@keyframes typing{
  from{width: 0;}
  to{width: 100%;}
}


</style>
