<template>
  <v-app>
    <v-container>
      <v-row align="center" no-gutters>
        <v-col cols="12" sm="12" lg="6">
          <v-row style="width: 100%" justify="center">
            <img style="max-width: 200px" :src="coin_image" />
          </v-row>
        </v-col>
        <v-col cols="12" sm="12" lg="6" class="pt-5">
          <Bar id="my-chart-id" :options="chartOptions" :data="chartData" />
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
                <td>
                  <v-img
                    :width="50"
                    aspect-ratio="16/9"
                    cover
                    class="my-1"
                    :src="item"
                  ></v-img>
                </td>
              </tr>
            </tbody>
          </v-table>
        </v-col>
      </v-row>
      <v-row align="center" justify="center" class="mb-10" no-gutters>
        <v-btn color="primary" @click="randomcoin" :disabled="loading"
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
const delay = (n) => new Promise((r) => setTimeout(r, n));
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
        localStorage.setItem("coinChartData", JSON.stringify(this.chartData));
      },
    },
    logKejadian: {
      deep: true,
      handler() {
        localStorage.setItem("logCoin", JSON.stringify(this.logKejadian));
      },
    },
  },
  mounted() {
    if (localStorage.getItem("coinChartData")) {
      this.chartData = JSON.parse(localStorage.getItem("coinChartData"));
    }
    if (localStorage.getItem("logCoin")) {
      this.logKejadian = JSON.parse(localStorage.getItem("logCoin"));
    }
  },
  data() {
    return {
      coin_image: "/assets/coin/coin_head.png",
      loading: false,
      chartData: {
        labels: ["Head", "Tail"],
        datasets: [
          {
            label: "Kejadian",
            backgroundColor: "#1867c0",
            data: [0, 0],
          },
        ],
      },
      chartOptions: {
        responsive: true,
      },
      logKejadian: [],
    };
  },
  methods: {
    clearData() {
      this.chartData = {
        labels: ["Head", "Tail"],
        datasets: [
          {
            label: "Kejadian",
            backgroundColor: "#1867c0",
            data: [0, 0],
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
    async randomcoin() {
      this.loading = true;
      var sides = ["head", "tail"];
      var animate_times = 10;
      var random_numbers = Math.floor(Math.random() * 2);
      var side;
      for (var i = 0; i < animate_times + random_numbers; i++) {
        await delay(100);
        side = i % 2;
        this.coin_image = `/assets/coin/coin_${sides[side]}.png`;
      }
      var tmp = JSON.parse(JSON.stringify(this.chartData));
      tmp.datasets[0].data[side]++;
      this.chartData = tmp;
      this.logKejadian.push(this.coin_image);
      this.loading = false;
    },
  },
};
</script>

<style></style>
