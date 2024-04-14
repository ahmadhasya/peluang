<template>
  <v-app>
    <v-container>
      <v-row align="center" no-gutters>
        <v-col cols="12" sm="12" lg="6">
          <v-row style="width: 100%" justify="center">
            <img style="max-width: 200px" :src="card_image" />
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
        <v-btn color="primary" @click="randomCard" :disabled="loading"
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
        localStorage.setItem("cardChartData", JSON.stringify(this.chartData));
      },
    },
    logKejadian: {
      deep: true,
      handler() {
        localStorage.setItem("logCard", JSON.stringify(this.logKejadian));
      },
    },
  },
  mounted() {
    if (localStorage.getItem("cardChartData")) {
      this.chartData = JSON.parse(localStorage.getItem("cardChartData"));
    }
    if (localStorage.getItem("logCard")) {
      this.logKejadian = JSON.parse(localStorage.getItem("logCard"));
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
      logKejadian: [],
    };
  },
  methods: {
    clearData() {
      this.chartData = {
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
      };
      this.logKejadian = [];
      this.loading = true;
      var that = this;
      setTimeout(function () {
        that.loading = false;
      }, 100);
    },
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
      var tmp = JSON.parse(JSON.stringify(this.chartData));
      tmp.datasets[random_types].data[random_numbers]++;
      this.chartData = tmp;
      this.logKejadian.push(this.card_image);
      this.loading = false;
    },
  },
};
</script>

<style></style>
