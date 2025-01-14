<script>
import { ref, onMounted } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

export default {
    setup() {
    const chartContainer = ref(null);

    onMounted(() => {
      const data = [1, 1, 2, 3, 5, 8, 13, 21];
      const width = 400;
      const height = 400;
      const radius = Math.min(width, height) / 2;

      const color = d3.scaleOrdinal()
        .domain(data)
        .range(d3.schemeCategory10);

      const pie = d3.pie().padAngle(0.01).startAngle(0).endAngle(2 * Math.PI);
      const arcs = pie(data);

      const arc = d3.arc()
        .innerRadius(100)
        .outerRadius(radius);

      const svg = d3.select(chartContainer.value)
        .select('svg')
        .attr('width', width)
        .attr('height', height)
        .append('g')
        .attr('transform', `translate(${width / 2}, ${height / 2})`);

      svg.selectAll('path')
        .data(arcs)
        .enter()
        .append('path')
        .attr('d', arc)
        .attr('fill', d => color(d.data));

      svg.selectAll('text')
        .data(arcs)
        .enter()
        .append('text')
        .attr('transform', d => `translate(${arc.centroid(d)})`)
        .attr('text-anchor', 'middle')
        .text(d => d.data);
    });

    return { chartContainer };
  }
};

</script>

<template>
    <main>
        <div ref="chartContainer">
            <svg></svg>
        </div>
    </main>
</template>

<style scoped>
  main {
      width: 100%;
      display: flex;
      justify-content: center;
  }
</style>