<template>
  <v-app>
    <v-container>
      <v-row align="center" no-gutters>
        <v-col cols="12" sm="12" lg="6">
          <v-row style="width: 100%" justify="center">
            <FortuneWheel
              style="width: 500px; max-width: 100%"
              :verify="canvasVerify"
              :canvas="canvasOptions"
              :prizes="prizesCanvas"
              @rotateStart="onCanvasRotateStart"
              @rotateEnd="onRotateEnd"
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
                v-for="(item, index) in logKejadian.slice().reverse()"
                :key="index"
              >
                <td>{{ logKejadian.length - index }}</td>
                <td>
                  <v-chip :color="item.bgColor" variant="elevated">{{ item.name }}</v-chip>
                </td>
              </tr>
            </tbody>
          </v-table>
        </v-col>
      </v-row>
      <v-row align="center" justify="center" class="mb-10" no-gutters>
        <v-btn color="red" class="ml-3" @click="clearData" :disabled="loading"
          >bersihkan</v-btn
        >
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import { ref, computed, onMounted } from "vue";
import FortuneWheel from "vue-fortune-wheel";
import "vue-fortune-wheel/style.css";

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
  components: { Bar, FortuneWheel },
  watch: {
    chartData: {
      deep: true,
      handler() {
        localStorage.setItem("wheelChartData", JSON.stringify(this.chartData));
      },
    },
    logKejadian: {
      deep: true,
      handler() {
        localStorage.setItem("logWheel", JSON.stringify(this.logKejadian));
      },
    },
  },
  mounted() {
    if (localStorage.getItem("wheelChartData")) {
      this.chartData = JSON.parse(localStorage.getItem("wheelChartData"));
    }
    if (localStorage.getItem("logWheel")) {
      this.logKejadian = JSON.parse(localStorage.getItem("logWheel"));
    }
  },
  data() {
    return {
      canvasVerify: ref(false), // Whether the turntable in canvas mode is enabled for verification
      canvasOptions: {
        btnWidth: 140,
        borderColor: "#584b43",
        borderWidth: 6,
        lineHeight: 30,
      },
      prizesCanvas: [
        {
          id: 0, //* The unique id of each prize, an integer greater than 0
          name: "Biru", // Prize name, display value when type is canvas (this parameter is not needed when type is image)
          value: "Biru", //* Prize value, return value after spinning
          bgColor: "#03A9F4", // Background color (no need for this parameter when type is image)
          color: "#ffffff", // Font color (this parameter is not required when type is image)
          probability: 25, //* Probability, up to 4 decimal places (the sum of the probabilities of all prizes
        },
        {
          id: 1,
          name: "Merah",
          value: "Merah",
          bgColor: "#F44336",
          color: "#ffffff",
          probability: 25,
        },
        {
          id: 2,
          name: "Kuning",
          value: "Kuning",
          bgColor: "#FFEB3B",
          color: "#ffffff",
          probability: 25,
        },
        {
          id: 3,
          name: "Hijau",
          value: "Hijau",
          bgColor: "#4CAF50",
          color: "#ffffff",
          probability: 25,
        },
      ],
      loading: false,
      chartData: {
        labels: ["Kejadian"],
        datasets: [
          {
            label: "Biru",
            backgroundColor: "#03A9F4",
            data: [0],
          },
          {
            label: "Merah",
            backgroundColor: "#F44336",
            data: [0],
          },
          {
            label: "Kuning",
            backgroundColor: "#FFEB3B",
            data: [0],
          },
          {
            label: "Hijau",
            backgroundColor: "#4CAF50",
            data: [0],
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
        labels: ["Kejadian"],
        datasets: [
          {
            label: "Biru",
            backgroundColor: "#03A9F4",
            data: [0],
          },
          {
            label: "Merah",
            backgroundColor: "#F44336",
            data: [0],
          },
          {
            label: "Kuning",
            backgroundColor: "#FFEB3B",
            data: [0],
          },
          {
            label: "Hijau",
            backgroundColor: "#4CAF50",
            data: [0],
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
    onCanvasRotateStart(rotate) {
      var that = this;
      if (that.canvasVerify.value) {
        const verified = true; // true: the test passed the verification, false: the test failed the verification
        testRequest(verified, verifyDuration * 1000).then((verifiedRes) => {
          if (verifiedRes) {
            console.log("Verification passed, start to rotate");
            rotate(); // Call the callback, start spinning
            that.canvasVerify.value = false; // Turn off verification mode
          } else {
            alert("Failed verification");
          }
        });
        return;
      }
      console.log("onCanvasRotateStart");
    },
    onRotateEnd(prize) {
      var tmp = JSON.parse(JSON.stringify(this.chartData));
      tmp.datasets[prize.id].data[0]++;
      this.chartData = tmp;
      this.logKejadian.push(prize);
    },
  },
};
</script>

<style></style>
