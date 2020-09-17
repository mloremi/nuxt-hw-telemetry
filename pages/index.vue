<template>
  <div class="container">
    <LineChart :chart-data="temperatureChartData" chart-id="temperature-chart" :width="1020"/>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import papa, { ParseResult } from 'papaparse'
import axios from 'axios'
import { Line }  from 'vue-chartjs'
import { ChartData, ChartDataSets } from 'chart.js'

export default Vue.extend({
  extends: Line,
  // asyncData() {
  //   return axios.get('/OpenHardwareMonitorLog-2020-09-02.csv').then((val) => { return val.data } );
  // },
  async asyncData({ params }) {
    const { data } = await axios.get('/OpenHardwareMonitorLog-2020-09-15.csv')
    return { _chartData: data }
  },
  data () {
    return {chartData: {} as any}
  },
  computed: {
    temperatureChartData(): {} {
      let pParsed: ParseResult<any> = papa.parse(this.$data._chartData);
      // this.chartData = pParsed.data;
      console.log("CSV: ", this.$data._chartData);
      console.log('CONVERTED TO JSON: ', pParsed.data[2]);

      let headerIndex: any[] = [];
      const temperatureRegex: RegExp = /temperature/g;
      headerIndex = this.findDataIndex(pParsed.data[0], pParsed.data[1], temperatureRegex);

      console.log(headerIndex);

      // create datasets
      return this.createDataSet(pParsed.data, headerIndex);
      
    }
  },
  methods: {
    findDataIndex(source: [], labelSource: [],  filter: RegExp): any[] {
      let headerIndex: any[] = [];
      source.forEach((el: string, index: number) => {
        if ( el.match(filter) ) {
          headerIndex.push({index, 'label': labelSource[index]});
        }
      });
      return headerIndex;
    },
    createDataSet(source: object[], dataIndex: Array<any>) {
      
      let lineColors = [''];
      let dataSet: ChartData = {
        labels: [],
        datasets: []
      };
      let chartData: any = [];

      chartData = dataIndex.map((el: any, index: number) => {
        let _dataSet: ChartDataSets = {
          label: el.label,
          borderColor: `#${Math.floor(Math.random()*16777215).toString(16)}`,
          fill: false,
          data: []
        };
        return _dataSet;
      });

      source.forEach((el: any, index: number) => {
        if ( index < 2 ) return; // skip 1st 2 index as their headers
        if ( index % 120 > 0) return 
        dataIndex.forEach((hel: any, hindex: number) => {
          let _data: any = {};
          _data.x = new Date(el[0]); // push date
          _data.y = el[hel.index];
          chartData[hindex].data.push(_data);
        });
        // _data.y = el[dataIndex[7].index];
        // chartData[7].data.push(_data);
      });

      // chartData = dataIndex.map((el: any, index: number) => {
      //   return {
      //     label: 'Data One',
      //     backgroundColor: '#f87979',
      //     data: [Math.floor(Math.random() * (50 - 5 + 1)) + 5, Math.floor(Math.random() * (50 - 5 + 1)) + 5]
      //   }
      // });

      console.log(chartData);
      dataSet.datasets = chartData;
      return dataSet;
    }
  },
  mounted() {
    
  }
})
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh - 90px;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family:
    'Quicksand',
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
