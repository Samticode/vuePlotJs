<script>
import { ref, onMounted, nextTick } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

export default {
    setup() {
        const chartContainer = ref(null);

        onMounted(() => {
            nextTick(() => {
                const margin = { top: 20, right: 20, bottom: 25, left: 30 };
                const width = chartContainer.value.offsetWidth - margin.left - margin.right;
                const height = chartContainer.value.offsetHeight - margin.top - margin.bottom;

                const svg = d3.select(chartContainer.value)
                    .append('svg')
                    .attr('width', '100%')
                    .attr('height', '100%')
                    .attr('viewBox', `0 0 ${width + margin.left + margin.right} ${height + margin.top + margin.bottom}`)
                    .attr('preserveAspectRatio', 'xMidYMid meet')
                    .append('g')
                    .attr('transform', `translate(${margin.left}, ${margin.top})`);

                const csvUrl = new URL('../assets/csv/heightAndWeight.csv', import.meta.url).href;
                d3.csv(csvUrl).then(data => {
                    data.forEach(d => {
                        d.Height = +d['Height(cm)'];
                        d.Weight = +d['Weight(kg)'];
                    });

                    const x = d3.scaleLinear()
                        .domain([d3.min(data, d => d.Weight), d3.max(data, d => d.Weight)])
                        .range([0, width]);
                    svg.append('g')
                        .attr('transform', `translate(0, ${height})`)
                        .call(d3.axisBottom(x));

                    const y = d3.scaleLinear()
                        .domain([d3.min(data, d => d.Height), d3.max(data, d => d.Height)])
                        .range([height, 0]);
                    svg.append('g')
                        .call(d3.axisLeft(y));

                    svg.append('g')
                    .selectAll('dot')
                    .data(data)
                    .enter()
                    .append('circle')
                    .attr('cx', d => x(d.Weight))
                    .attr('cy', d => y(d.Height))
                    .attr('r', 2.5)
                    .style('fill', '#69b3a2');

                });
            });
        });

        return { chartContainer };
    }
}
</script>

<template>
    <main>
        <div ref="chartContainer"></div>
    </main>
</template>

<style scoped>
main {
    width: 100%;
    display: flex;
    justify-content: center;
}

div {
    width: 50%;
    height: 400px;
    border: solid 1px black;
}

svg {
    width: 100%;
    height: 100%;
}
</style>
