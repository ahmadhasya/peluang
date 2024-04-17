<template>
  <v-app>
    <v-container class="mb-6">
      <v-row align="center" no-gutters>
        <v-col cols="12" sm="12" lg="6">
          <div id="dice-box"></div>
        </v-col>
        <v-col cols="12" sm="12" lg="6">
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
        <v-col cols="12" sm="12" lg="6">
          <v-select
            label="Tipe"
            :items="dice_types"
            item-title="name"
            item-value="index"
            v-model="type"
            @update:model-value="clearData()"
          ></v-select>
        </v-col>
        <v-col cols="12" sm="12" lg="6" class="px-5">
          <v-btn color="primary" @click="rollDice" :disabled="loading"
            >Acak {{ tries }}x</v-btn
          >
          <v-btn color="red" class="ml-3" @click="clearData" :disabled="loading"
            >Bersihkan</v-btn
          >
        </v-col>
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
    type: {
      deep: true,
      handler() {
        localStorage.setItem("diceType", JSON.stringify(this.type));
      },
    },
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
            backgroundColor: "#335e3b",
            data: [0, 0, 0, 0, 0, 0],
          },
        ],
      },
      chartOptions: {
        responsive: true,
      },
      logKejadian: [],
      type: 0,
      dice_types: [
        {
          index: 0,
          name: "4 Sisi",
          labels: [1, 2, 3, 4],
        },
        {
          index: 1,
          name: "6 Sisi",
          labels: [1, 2, 3, 4, 5, 6],
        },
        {
          index: 2,
          name: "8 Sisi",
          labels: [1, 2, 3, 4, 5, 6, 7, 8],
        },
      ],
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
    } else {
      this.clearData();
    }

    if (localStorage.getItem("logDice")) {
      this.logKejadian = JSON.parse(localStorage.getItem("logDice"));
    }

    if (localStorage.getItem("diceType")) {
      this.type = JSON.parse(localStorage.getItem("diceType"));
    }
  },
  methods: {
    clearData() {
      var that = this;
      var data_init = [];
      this.dice_images = [];

      var labels = this.dice_types[this.type].labels;

      labels.forEach(() => {
        data_init.push(0);
      });

      this.chartData = {
        labels: labels,
        datasets: [
          {
            label: "Kejadian",
            backgroundColor: "#335e3b",
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
    rollDice() {
      this.loading = true;
      diceBox.roll("1d" + this.dice_types[this.type].labels.length);
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
