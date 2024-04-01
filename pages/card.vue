<template>
  <v-app>
    <v-container>
      <v-row align="center" no-gutters>
        <v-col sm="12" lg="6">
          <v-img
            :width="200"
            aspect-ratio="16/9"
            cover
            :src="card_image"
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
        <v-btn color="primary" @click="randomCard" v-if="!loading">Roll {{ tries }}x</v-btn>
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
        localStorage.setItem("cardChartData", JSON.stringify(this.chartData));
      },
    },
  },
  mounted(){
    if (localStorage.getItem("cardChartData")) {
      this.chartData = JSON.parse(localStorage.getItem("cardChartData"));
    }
  },
  data() {
    return {
      card_image: "/assets/aces/ace_of_hearts.png",
      loading: false,
      chartData: {
        labels: ["Ace", 2, 3, 4, 5, 6, 7, 8, 9, 10, "Jack", "Queen", "King"],
        datasets: [
          {
            label: "Heart",
            backgroundColor: "red",
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
          {
            label: "Club",
            backgroundColor: "black",
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
          {
            label: "Diamond",
            backgroundColor: "pink",
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
          {
            label: "Spade",
            backgroundColor: "gray",
            data: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
          },
        ],
      },
      chartOptions: {
        responsive: true,
      },
    };
  },
  methods: {
    async randomCard() {
      this.loading = true;
      var numbers = [
        "ace",
        2,
        3,
        4,
        5,
        6,
        7,
        8,
        9,
        10,
        "jack",
        "queen",
        "king",
      ];
      var types = ["hearts", "clubs", "diamonds", "spades"];
      var animate_times = 10;
      var random_numbers;
      var random_types;

      for (var i = 0; i < animate_times; i++) {
        random_numbers = Math.floor(Math.random() * numbers.length);
        random_types = Math.floor(Math.random() * types.length);

        await delay(100);
        this.card_image = `/assets/aces/${numbers[random_numbers]}_of_${types[random_types]}.png`;
      }

      this.chartData.datasets[random_types].data[random_numbers]++;
      this.loading = false;
    },
  },
};
</script>

<style></style>
