<template>
  <d-card class="card-small h-100">
    <!-- Card Header -->
    <d-card-header class="border-bottom" style="display:flex">
      <h6 class="m-0" style="flex-grow:1">{{ title }}</h6>
      <d-select size="sm" value="week_values" style="width: 130px">
        <option value="week_values">7 days</option>
        <!-- <option value="month_values">30 days</option> -->
      </d-select>
    </d-card-header>

    <d-card-body class="pt-0">
      <canvas height="120" ref="canvas" style="max-width: 100% !important;"></canvas>
    </d-card-body>
  </d-card>
</template>

<script>
import Chart from '../../utils/chart'

const defaultChartData = {
  labels: Array.from(new Array(30), (_, i) => (i === 0 ? 1 : i)),
  datasets: [
    {
      label: 'Current Month',
      fill: 'start',
      data: [
        500,
        800,
        320,
        180,
        240,
        320,
        230,
        650,
        590,
        1200,
        750,
        940,
        1420,
        1200,
        960,
        1450,
        1820,
        2800,
        2102,
        1920,
        3920,
        3202,
        3140,
        2800,
        3200,
        3200,
        3400,
        2910,
        3100,
        4250
      ],
      backgroundColor: 'rgba(0,123,255,0.1)',
      borderColor: 'rgba(0,123,255,1)',
      pointBackgroundColor: '#ffffff',
      pointHoverBackgroundColor: 'rgb(0,123,255)',
      borderWidth: 1.5,
      pointRadius: 0,
      pointHoverRadius: 3
    }
  ]
}

export default {
  name: 'users-overview',
  props: {
    title: {
      type: String,
      default: 'Revenue'
    },
    chartData: {
      type: Object,
      default() {
        return defaultChartData
      }
    }
  },
  data() {
    return {
      dateRange: {
        from: null,
        to: null
      }
    }
  },
  mounted() {
    const chartOptions = {
      ...{
        responsive: true,
        legend: {
          position: 'top'
        },
        elements: {
          line: {
            // A higher value makes the line look skewed at this ratio.
            tension: 0.3
          },
          point: {
            radius: 0
          }
        },
        scales: {
          xAxes: [
            {
              gridLines: false,
              ticks: {
                callback(tick, index) {
                  // Jump every 7 values on the X axis labels to avoid clutter.
                  return index % 7 !== 0 ? '' : tick
                }
              }
            }
          ],
          yAxes: [
            {
              ticks: {
                suggestedMax: 45,
                callback(tick) {
                  if (tick === 0) {
                    return tick
                  }
                  // Format the amounts using Ks for thousands.
                  return tick > 999 ? `${(tick / 1000).toFixed(1)}K` : tick
                }
              }
            }
          ]
        },
        hover: {
          mode: 'nearest',
          intersect: false
        },
        tooltips: {
          custom: false,
          mode: 'nearest',
          intersect: false
        }
      },
      ...this.chartOptions
    }

    const BlogUsersOverview = new Chart(this.$refs.canvas, {
      type: 'LineWithLine',
      data: this.chartData,
      options: chartOptions
    })

    // They can still be triggered on hover.
    const buoMeta = BlogUsersOverview.getDatasetMeta(0)
    buoMeta.data[0]._model.radius = 0
    buoMeta.data[this.chartData.datasets[0].data.length - 1]._model.radius = 0

    // Render the chart.
    BlogUsersOverview.render()
  }
}
</script>

