<script>
import { ref, onMounted } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

export default {
  setup() {
    const dataset = [
      { name: 'A', value: 80 },
      { name: 'B', value: 100 },
      { name: 'C', value: 56 },
      { name: 'D', value: 120 },
      { name: 'E', value: 180 },
      { name: 'F', value: 30 },
      { name: 'G', value: 40 },
      { name: 'H', value: 120 },
      { name: 'I', value: 160 }
    ];

    onMounted(() => {
      const svg = d3.select('svg')
        .attr('width', 800)
        .attr('height', 400);

      const margin = { top: 20, right: 30, bottom: 40, left: 40 };
      const width = +svg.attr('width') - margin.left - margin.right;
      const height = +svg.attr('height') - margin.top - margin.bottom;

      const x = d3.scaleBand()
        .domain(dataset.map(d => d.name))
        .range([margin.left, width - margin.right])
        .padding(0.1);

      const y = d3.scaleLinear()
        .domain([0, d3.max(dataset, d => d.value)])
        .nice()
        .range([height - margin.bottom, margin.top]);

      const xAxis = g => g
        .attr('transform', `translate(0,${height - margin.bottom})`)
        .call(d3.axisBottom(x).tickSizeOuter(0));

      const yAxis = g => g
        .attr('transform', `translate(${margin.left},0)`)
        .call(d3.axisLeft(y))
        .call(g => g.select('.domain').remove());

      svg.append('g')
        .selectAll('rect')
        .data(dataset)
        .join('rect')
        .attr('x', d => x(d.name))
        .attr('y', d => y(d.value))
        .attr('height', d => y(0) - y(d.value))
        .attr('width', x.bandwidth())
        .attr('fill', 'steelblue');

      svg.append('g')
        .call(xAxis);

      svg.append('g')
        .call(yAxis);
    });
  }
}
</script>

<template>
  <svg></svg>
</template>

<style scoped>
svg {
  display: block;
  margin: auto;
}
</style>