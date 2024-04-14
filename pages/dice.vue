<template>
  <v-app>
    <v-container class="mb-6">
      <v-row align="center" no-gutters>
        <v-col cols="12" sm="12" lg="6">
          <div id="dice-box"></div>
        </v-col>
        <v-col cols="12" sm="12" lg="6">
          <Bar
            id="my-chart-id"
            :options="chartOptions"
            :data="chartData"
          />
          <v-table height="200px" fixed-header>
            <thead>
              <tr>
                <th>Number</th>
                <th>Value</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(item, index) in logKejadian.slice().reverse()"
                :key="index"
              >
                <td>{{ logKejadian.length - index }}</td>
                <td>{{ item }}</td>
              </tr>
            </tbody>
          </v-table>
        </v-col>
      </v-row>
      <v-row align="center" justify="center" class="mb-5" no-gutters>
        <v-btn color="primary" @click="rollDice" :disabled="loading"
          >Roll {{ tries }}x</v-btn
        >
        <v-btn color="red" class="ml-3" @click="clearData" :disabled="loading"
          >Clear Logs</v-btn
        >
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
  computed: {
    tries() {
      var total = 0;
      this.chartData.datasets.forEach((element) => {
        total += element.data.reduce((partialSum, a) => partialSum + a, 0);
      });
      return total;
    },
  },
  watch: {
    chartData: {
      deep: true,
      handler() {
        localStorage.setItem("diceChartData", JSON.stringify(this.chartData));
      },
    },
    logKejadian: {
      deep: true,
      handler() {
        localStorage.setItem("logDice", JSON.stringify(this.logKejadian));
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
      logKejadian: [],
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
      var tmp = JSON.parse(JSON.stringify(this.chartData));
      tmp.datasets[0].data[dieResult.value - 1] += 1;
      that.chartData = tmp;
      that.logKejadian.push(dieResult.value);
    };
    if (localStorage.getItem("diceChartData")) {
      this.chartData = JSON.parse(localStorage.getItem("diceChartData"));
    }
    if (localStorage.getItem("logDice")) {
      this.logKejadian = JSON.parse(localStorage.getItem("logDice"));
    }
  },
  methods: {
    clearData() {
      this.chartData = {
        labels: ["1", "2", "3", "4", "5", "6"],
        datasets: [
          {
            label: "Kejadian",
            backgroundColor: "#1867c0",
            data: [0, 0, 0, 0, 0, 0],
          },
        ],
      };
      this.logKejadian = [];
      this.loading = true;
      var that = this;
      setTimeout(function () {
        that.loading = false;
      }, 100);
    },
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
  height: 40vh;
}
</style>
