<template>
  <div id="app">
    <h1 class="mini-val-d"> d  = {{dValuedeg}} degrees {{dValuerad}} radians (translational distance in z axis)</h1>
    <h1 class="mini-val-a"> a  = {{aValuedeg}} degrees {{aValuerad}} radians (angle of rotation in yz plane)</h1>

    <div class="viz-wrapper">
      <div>
        <p>drag the cones to change the perspective</p>
        <HelloWorld msg="Welcome to Your Vue.js App" v-on:sendPoints="updateCoordinates"/>

      </div>
      <Plotly :data="data" :layout="layout" :display-mode-bar="false"></Plotly>
    </div>
    <table >
      <tr>
        <th></th>
        <th>X</th>
        <th>Y</th>
      <tr v-for="(item, index) in fullCord[0]" :key="item.id">>
        <td>{{item.toFixed(2)}}</td>
        <td>{{fullCord[1][index].toFixed(2)}}</td>
      </tr>
    </table>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import { Plotly } from 'vue-plotly';



export default {
  name: 'App',
  components: {
    HelloWorld,
    Plotly
  },
  computed: {
    fullCord: function () {
      return [this.data[0].x , this.data[0].y]
    }
  },
  data: function(){
    return {
      aValuedeg: 90,
      dValuedeg: 0,
      aValuerad: 1.57,
      dValuerad: 0,
      data:[{
        x: [],
        y: [],
        type:"scatter"
      }],
      layout:{
        title: "Conic Section",
        xaxis: {
          range: [-250, 250],
          type: 'linear'
        },
        yaxis: {
          range: [-250, 250],
          type: 'linear'
        },
        height:600,
        width:600,
      },
      config: {responsive: true}
    }
  },
  methods: {
    updateCoordinates: function (coor) {

      this.data[0].y = coor[0];
      this.data[0].x = coor[1];
      this.aValuedeg = coor[2].toFixed(2);
      this.dValuedeg = coor[3].toFixed(2);
      this.aValuerad = coor[4].toFixed(2);
      this.dValuerad= coor[5].toFixed(2);
    }
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

.viz-wrapper {
  display: flex;
  justify-content: space-around;
  width: 80%;
  margin: 0 auto;
}

table {
  width: 500px;
  font-size: 10px;
  margin: 0 auto;
  border: 1px solid black;
}

.mini-val-d {
  position: absolute;
  font-size: 14px;
  top: 0px;
  left: 100px;
}
.mini-val-a {
  position: absolute;
  font-size: 14px;
  top: 20px;
  left: 100px;
}
</style>
