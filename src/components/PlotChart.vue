<!-- src/components/PlotChart.vue -->
<template>
  <div ref="chartContainer"></div>
</template>

<script>
import { ref, onMounted } from 'vue';
import * as Plot from '@observablehq/plot';

const aapl = [
  {Date: new Date("2013-06-01"), Open: 64.501427, High: 65.414284, Low: 20.000000, Close: 30.962860, Volume: 79237200},
  {Date: new Date("2013-06-02"), Open: 30.835716, High: 40.028572, Low: 25.164288, Close: 35.408573, Volume: 111779500},
  {Date: new Date("2013-06-03"), Open: 35.737144, High: 50.000000, Low: 20.337143, Close: 45.264286, Volume: 185403400},
  {Date: new Date("2013-06-04"), Open: 40.462856, High: 50.549999, Low: 20.842857, Close: 48.082859, Volume: 150801000},
  {Date: new Date("2013-06-05"), Open: 50.721428, High: 55.869999, Low: 25.572857, Close: 52.894287, Volume: 106976100},
  {Date: new Date("2013-06-06"), Open: 30.500000, High: 45.000000, Low: 20.500000, Close: 35.000000, Volume: 98500000},
  {Date: new Date("2013-06-07"), Open: 35.100000, High: 50.200000, Low: 30.000000, Close: 45.800000, Volume: 101200000},
  {Date: new Date("2013-06-08"), Open: 40.700000, High: 60.500000, Low: 25.200000, Close: 55.300000, Volume: 102500000},
  {Date: new Date("2013-06-09"), Open: 45.400000, High: 60.000000, Low: 20.000000, Close: 50.900000, Volume: 105000000},
  {Date: new Date("2013-06-10"), Open: 50.800000, High: 65.500000, Low: 25.500000, Close: 60.300000, Volume: 108000000},
  {Date: new Date("2013-06-11"), Open: 55.100000, High: 70.000000, Low: 30.000000, Close: 65.800000, Volume: 112000000},
  {Date: new Date("2013-06-12"), Open: 60.700000, High: 75.000000, Low: 35.500000, Close: 70.200000, Volume: 115000000},
  {Date: new Date("2013-06-13"), Open: 65.100000, High: 80.000000, Low: 40.000000, Close: 75.800000, Volume: 120000000},
  {Date: new Date("2013-06-14"), Open: 70.700000, High: 85.200000, Low: 45.500000, Close: 80.000000, Volume: 125000000},
  {Date: new Date("2013-06-15"), Open: 75.000000, High: 90.800000, Low: 50.800000, Close: 85.500000, Volume: 130000000},
  {Date: new Date("2013-06-16"), Open: 80.400000, High: 95.000000, Low: 55.200000, Close: 90.800000, Volume: 135000000},
  {Date: new Date("2013-06-17"), Open: 85.700000, High: 100.200000, Low: 60.500000, Close: 95.900000, Volume: 140000000}
];

const goog = [
  {Date: new Date("2013-06-01"), Open: 120.501427, High: 130.414284, Low: 80.000000, Close: 100.962860, Volume: 89237200},
  {Date: new Date("2013-06-02"), Open: 100.835716, High: 120.028572, Low: 85.164288, Close: 105.408573, Volume: 121779500},
  {Date: new Date("2013-06-03"), Open: 110.737144, High: 140.000000, Low: 80.337143, Close: 115.264286, Volume: 195403400},
  {Date: new Date("2013-06-04"), Open: 115.462856, High: 145.549999, Low: 90.842857, Close: 120.082859, Volume: 160801000},
  {Date: new Date("2013-06-05"), Open: 125.721428, High: 150.869999, Low: 95.572857, Close: 130.894287, Volume: 116976100},
  {Date: new Date("2013-06-06"), Open: 100.500000, High: 135.000000, Low: 85.500000, Close: 110.000000, Volume: 108500000},
  {Date: new Date("2013-06-07"), Open: 115.100000, High: 145.200000, Low: 95.000000, Close: 125.800000, Volume: 111200000},
  {Date: new Date("2013-06-08"), Open: 120.700000, High: 155.500000, Low: 90.200000, Close: 135.300000, Volume: 112500000},
  {Date: new Date("2013-06-09"), Open: 135.400000, High: 160.000000, Low: 80.000000, Close: 145.900000, Volume: 115000000},
  {Date: new Date("2013-06-10"), Open: 140.800000, High: 165.500000, Low: 85.500000, Close: 150.300000, Volume: 118000000},
  {Date: new Date("2013-06-11"), Open: 150.100000, High: 170.000000, Low: 90.000000, Close: 155.800000, Volume: 122000000},
  {Date: new Date("2013-06-12"), Open: 160.700000, High: 175.000000, Low: 95.500000, Close: 160.200000, Volume: 125000000},
  {Date: new Date("2013-06-13"), Open: 170.100000, High: 180.000000, Low: 100.000000, Close: 165.800000, Volume: 130000000},
  {Date: new Date("2013-06-14"), Open: 180.700000, High: 185.200000, Low: 105.500000, Close: 170.000000, Volume: 135000000},
  {Date: new Date("2013-06-15"), Open: 190.000000, High: 190.800000, Low: 110.800000, Close: 175.500000, Volume: 140000000},
  {Date: new Date("2013-06-16"), Open: 200.400000, High: 195.000000, Low: 115.200000, Close: 180.800000, Volume: 145000000},
  {Date: new Date("2013-06-17"), Open: 210.700000, High: 200.200000, Low: 120.500000, Close: 185.900000, Volume: 150000000}
];


export default {
  setup() {
    const chartContainer = ref(null);

    onMounted(() => {
    //   const chart = Plot.plot({
    //     marks: [
    //         Plot.frame(),
    //         Plot.text(["Hello, world!"], {frameAnchor: "middle"})
    //     ]
    //   });

    // const chart = Plot.plot({
    //     marks: [
    //         Plot.ruleY([0]),
    //         Plot.lineY(aapl, {x: "Date", y: "Close", stroke: "red"}),
    //         Plot.lineY(goog, {x: "Date", y: "Close", stroke: "blue"}),

    //     ]
    // });

    const chart = Plot.plot({
        marks: [
            Plot.ruleY([0]),
            Plot.lineY(aapl, {x: "Date", y: "Close", stroke: "red", title: "AAPL"}),
            Plot.lineY(goog, {x: "Date", y: "Close", stroke: "blue", title: "GOOG"}),
            Plot.text(aapl, {x: "Date", y: "Close", text: "AAPL", fill: "red", dy: -10}),
            Plot.text(goog, {x: "Date", y: "Close", text: "GOOG", fill: "blue", dy: -10}),
        ]
    });



      chartContainer.value.appendChild(chart);
    });

    return { chartContainer };
  }
};
</script>

<style scoped>
div {
  max-width: 600px;
  margin: auto;
}
</style>
