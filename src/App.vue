<template>
  <div id="app">
    <h3><strong>Most popular airports by month:</strong></h3>
    <div class="Chart">
      <doughnut-chart 
        ref="best_airports_chart" 
        :chart-data="chartData" 
        :options="options">
      </doughnut-chart>
      <div v-for="(value, index) in currentDataSet" :key="index">
        <span>
        {{ monthsArr[index] + " => " + currentDataSet[index] }} reservations
        </span>
        <input 
          type="text" 
          :value="chartData.labels[index]" 
          @input="updateName($event.target.value, index)"
        >
        <button @click="remove(index)">remove</button>
      </div>
    </div>
    <br/>
    <button class="button" @click="addExperience('oke', 1)">Add Airport</button>
  </div>
</template>

<script>
import DoughnutChart from '@/components/DoughnutChart';
import randomColor from 'randomcolor';
import axios from 'axios';

const apiURL = "https://my-json-server.typicode.com/ariel8300/samplejsonapi/db";

const options = {
  responsive: true,
  maintainAspectRatio: false,
  animation: {
    animateRotate: false
  }
};

export default {
  name: 'App',
  components: {
    "doughnut-chart": DoughnutChart,
  },
  data() {
    return {
      options,
      chartData: {
        labels: [],
        datasets: [
          {
            backgroundColor: [randomColor()],
            data: []
          }
        ]
      },
      airports: null,
      loaded: false,
      monthsArr: ['January', 'February', 'March', 'April', 'May',
       'June', 'July', 'August', 'September', 'October', 'November', 'December']
    }
  },
  async created() {
    await axios.get(apiURL)
    .then(res => {
      this.loaded = true;
      let { bestAirports } = JSON.parse(JSON.stringify(res.data));
      this.airports = bestAirports;
    })
    .then(this.addAirportsToChart);
  },
  computed: {
    currentDataSet() {
      return this.chartData.datasets[0].data;
    }
  },
  methods: {
    updateChart() {
      this.$refs.best_airports_chart.update();
    },
    updateAmount(amount, index) {
      this.chartData.datasets[0].data.splice(index, 1, amount);
      this.updateChart();
    },
    updateName (text, index) {
      this.chartData.labels.splice(index, 1, text);
      this.updateChart();
    },
    addExperience(name, amount) {
      const currentDataset = this.chartData.datasets[0];
      this.chartData.labels.push(name);
      currentDataset.data.push(amount);
      currentDataset.backgroundColor.push(randomColor());
      this.updateChart();
    },
    remove (index) {
      const currentDataset = this.chartData.datasets[0];
      currentDataset.data.splice(index, 1);
      this.chartData.labels.splice(index, 1);
      currentDataset.backgroundColor.splice(index, 1);
      this.updateChart();
    },
    addAirportsToChart() {
      for(let curr = 0; curr < this.airports.length; curr++) {
        this.addExperience(this.airports[curr].airport.name, this.airports[curr].amount);
      }
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
</style>
