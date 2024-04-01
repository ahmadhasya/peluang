<template>
  <v-app>
    <v-container>
      <v-row align="center" no-gutters>
        <v-col sm="12" lg="6">
          <v-img
            :width="200"
            aspect-ratio="16/9"
            cover
            :src="coin_image"
          ></v-img>
        </v-col>
        <v-col sm="12" lg="6">
          <Bar
            id="my-chart-id"
            v-if="!loading"
            :options="chartOptions"
            :data="chartData"
          />
        </v-col>
      </v-row>
      <v-row align="center" justify="center" no-gutters>
        <v-btn color="primary" @click="randomcoin" v-if="!loading">Roll {{ tries }}x</v-btn>
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
        localStorage.setItem("coinChartData", JSON.stringify(this.chartData));
      },
    },
  },
  mounted() {
    if (localStorage.getItem("coinChartData")) {
      this.chartData = JSON.parse(localStorage.getItem("coinChartData"));
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
    };
  },
  methods: {
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

      this.chartData.datasets[0].data[side]++;
      this.loading = false;
    },
  },
};
</script>

<style></style>
