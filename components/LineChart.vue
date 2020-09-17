<script>
import Vue from 'vue'
import { Line, mixins } from 'vue-chartjs'
const { reactiveProp } = mixins

export default Vue.extend({
  extends: Line,
  mixins: [reactiveProp],
  // props: ['options'],
  mounted () {
    console.log("LineChart:", this.chartData);
    let options = {
      fill: false,
      animation: {
        duration: 0
      },
      scales: {
            xAxes: [{
                type: 'time',
                time: {
                    unit: 'hour',
                    distribution: 'series'
                },
                ticks: {
                  source: 'auto',
                  min: new Date('09/12/2020 00:00:01')
                },
            },
            ]
        }
    }
    this.renderChart(this.chartData, options)
  }
})

</script>

<style>

</style>