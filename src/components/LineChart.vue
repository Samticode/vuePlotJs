<script>
import { ref, onMounted } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

export default {
    setup() {
        const chartContainer = ref(null);

        onMounted(() => {
            const margin = { top: 20, right: 20, bottom: 30, left: 60 };
            const width = 800 - margin.left - margin.right;
            const height = 400 - margin.top - margin.bottom;

            const svg = d3.select(chartContainer.value)
                .append('svg')
                .attr('width', width + margin.left + margin.right)
                .attr('height', height + margin.top + margin.bottom)
                .append('g')
                .attr('transform', `translate(${margin.left}, ${margin.top})`);

            const csvUrl = new URL('../assets/csv/norwayPopulation.csv', import.meta.url).href;
            d3.csv(csvUrl)
            .then(data => {
                //sette variabler
                data.forEach(d => {
                    d.Year = d3.timeParse("%Y")(d.Year);
                    d.Value = +d.Value;
                });

                // sette x-axis tags og width
                const x = d3.scaleTime()
                    .domain(d3.extent(data, d => d.Year))
                    .range([0, width]);

                // sette y-axis tags og height og starte fra en nærmere value enn 0
                const y = d3.scaleLinear()
                    .domain([d3.min(data, d => d.Value) - 100000, d3.max(data, d => d.Value)])
                    .range([height, 0]);

                //line gjør livet lettere
                const line = d3.line()
                    .x(d => x(d.Year))
                    .y(d => y(d.Value));

                // sette at det viser hvert 10 år og vertical lines                      
                svg.append('g')
                    .attr('transform', `translate(0, ${height})`)
                    .call(d3.axisBottom(x).ticks(d3.timeYear.every(10)).tickFormat(d3.timeFormat("%Y")))
                    .call(g => g.selectAll(".tick line")
                        .clone()
                        .attr("y2", -height)
                        .attr("stroke-opacity", 0.1));

                // sette horizontal lines
                svg.append('g')
                    .call(d3.axisLeft(y))
                    .call(g => g.selectAll(".tick line")
                        .clone()
                        .attr("x2", width)
                        .attr("stroke-opacity", 0.1));

                svg.append('path')
                    .datum(data)
                    .attr('fill', 'none')
                    .attr('stroke', 'steelblue')
                    .attr('stroke-width', 1.5)
                    .attr('d', line);
            })
            .catch(error => {
                console.error('Error loading or parsing data:', error);
            });
        });

        return { chartContainer };
    }
};
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
</style>