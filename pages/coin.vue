<template>
  <v-app>
    <v-container class="mb-10">
      <v-row align="center" no-gutters>
        <v-col cols="12" sm="12" lg="6">
          <v-row style="width: 100%" justify="center">
            <img
              v-for="(item, index) in coin_images"
              style="max-width: 200px"
              :src="item"
              :key="index + item"
            />
          </v-row>
        </v-col>
        <v-col cols="12" sm="12" lg="6" class="pt-5">
          <Bar id="my-chart-id" :options="chartOptions" :data="chartData" />
          <v-table height="200px" fixed-header>
            <thead>
              <tr>
                <th>Urutan</th>
                <th>Nilai</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(item_log, index) in logKejadian.slice().reverse()"
                :key="index"
              >
                <td>{{ logKejadian.length - index }}</td>
                <td>
                  <img
                    v-for="image in item_log"
                    :width="50"
                    aspect-ratio="16/9"
                    cover
                    class="my-1"
                    :src="image"
                    :key="image"
                  />
                </td>
              </tr>
            </tbody>
          </v-table>
        </v-col>
      </v-row>
      <v-row align="center" justify="center" no-gutters>
        <v-col cols="12" sm="12" lg="6">
          <v-select
            label="Tipe"
            :items="coin_types"
            item-title="name"
            item-value="index"
            v-model="type"
            @update:model-value="clearData()"
          ></v-select>
        </v-col>
        <v-col cols="12" sm="12" lg="6" class="px-5">
          <v-btn color="primary" @click="roll" :disabled="loading"
            >Acak {{ tries }}x</v-btn
          >
          <v-btn color="red" class="ml-3" @click="clearData" :disabled="loading"
            >Bersihkan</v-btn
          >
        </v-col>
      </v-row>
      <v-row> </v-row>
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
    type: {
      deep: true,
      handler() {
        localStorage.setItem("coinType", JSON.stringify(this.type));
      },
    },
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
    } else {
      this.clearData();
    }

    if (localStorage.getItem("logCoin")) {
      this.logKejadian = JSON.parse(localStorage.getItem("logCoin"));
    }
    if (localStorage.getItem("coinType")) {
      this.type = JSON.parse(localStorage.getItem("coinType"));
    }
  },
  data() {
    return {
      coin_images: ["/assets/coin/coin_head.png"],
      loading: false,
      chartData: {
        labels: ["Angka", "Gambar"],
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
      type: 0,
      coin_types: [
        {
          index: 0,
          name: "1 Koin",
          labels: ["A", "G"],
        },
        {
          index: 1,
          name: "2 Koin",
          labels: ["AA", "AG", "GA", "GG"],
        },
        {
          index: 2,
          name: "3 Koin",
          labels: ["AAA", "AAG", "AGA", "AGG", "GAA", "GAG", "GGA", "GGG"],
        },
      ],
    };
  },
  methods: {
    clearData() {
      var that = this;
      var data_init = [];
      this.coin_images = [];

      var labels = this.coin_types[this.type].labels;

      labels.forEach(() => {
        data_init.push(0);
      });

      this.chartData = {
        labels: labels,
        datasets: [
          {
            label: "Kejadian",
            backgroundColor: "#1867c0",
            data: data_init,
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
    async roll() {
      this.loading = true;
      var loop = this.type + 1;
      var coin_set = "";
      this.coin_images = [];
      for (var i = 0; i < loop; i++) {
        coin_set += await this.randomcoin(i);
      }

      var label_index;
      this.coin_types[this.type].labels.forEach((element, index) => {
        if (element === coin_set) {
          label_index = index;
          return;
        }
      });

      var tmp = JSON.parse(JSON.stringify(this.chartData));
      tmp.datasets[0].data[label_index]++;
      this.chartData = tmp;
      this.logKejadian.push(this.coin_images);
      this.loading = false;
    },
    async randomcoin(index) {
      var sides = ["head", "tail"];
      var animate_times = 10;
      var random_numbers = Math.floor(Math.random() * 2);
      var side_index;
      for (var i = 0; i < animate_times + random_numbers; i++) {
        await delay(100);
        side_index = i % 2;
        this.coin_images[index] = `/assets/coin/coin_${sides[side_index]}.png`;
      }
      return side_index == 0 ? "A" : "G";
    },
  },
};
</script>

<style></style>
