<template>
  <v-app>
    <v-container class="mb-6">
      <v-row align="center" no-gutters>
        <v-col id="dice-box" sm="12" lg="6"> </v-col>
        <v-col sm="12" lg="6">
          <Bar
            id="my-chart-id"
            v-if="!loading"
            :options="chartOptions"
            :data="chartData"
        /></v-col>
      </v-row>
      <v-row align="center" justify="center" no-gutters>
        <v-btn color="primary" @click="rollDice" v-if="!loading">Roll {{ tries }}x</v-btn>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import DiceBox from "@3d-dice/dice-box";
import { Bar } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

var diceBox;

export default {
  components: { Bar },
  computed:{
    tries(){
      var total = 0;
      this.chartData.datasets.forEach(element=>{
        total += element.data.reduce((partialSum, a) => partialSum + a, 0);
      });
      return total;
    }
  },
  watch: {
    chartData: {
      deep: true,
      handler() {
        localStorage.setItem("diceChartData", JSON.stringify(this.chartData));
      },
    },
  },
  data() {
    return {
      loading: false,
      chartData: {
        labels: ["1", "2", "3", "4", "5", "6"],
        datasets: [
          {
            label: "Kejadian",
            backgroundColor: "#1867c0",
            data: [0, 0, 0, 0, 0, 0],
          },
        ],
      },
      chartOptions: {
        responsive: true,
      },
    };
  },
  mounted() {
    var that = this;
    diceBox = new DiceBox("#dice-box", {
      assetPath: "/assets/", // required
      scale: 10,
    });
    diceBox.init();
    diceBox.onDieComplete = (dieResult) => {
      that.loading = false;
      that.chartData.datasets[0].data[dieResult.value - 1] += 1;
    };
    if (localStorage.getItem("diceChartData")) {
      this.chartData = JSON.parse(localStorage.getItem("diceChartData"));
    }
  },
  methods: {
    rollDice() {
      this.loading = true;
      diceBox.roll("1d6");
    },
  },
};
</script>

<style>
#dice-box canvas {
  width: 100%;
  height: 300px;
}
</style>
