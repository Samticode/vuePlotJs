<script>
import { ref, onMounted } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";
import { gsap } from 'gsap';
import { ScrollTrigger } from "gsap/dist/ScrollTrigger"; 

export default {
    setup() {
        gsap.registerPlugin(ScrollTrigger);
        const chartContainer = ref(null);
        const selectedRegions = ['California', 'NewYork', 'Tampa'];

        onMounted(() => {
            const margin = { top: 20, right: 30, bottom: 50, left: 70 };
            const width = chartContainer.value.clientWidth - margin.left - margin.right;
            const height = chartContainer.value.clientHeight - margin.top - margin.bottom;

            const svg = d3.select(chartContainer.value)
                .append('svg')
                .attr('width', width + margin.left + margin.right)
                .attr('height', height + margin.top + margin.bottom)
                .append('g')
                .attr('transform', `translate(${margin.left}, ${margin.top})`);

            const csvUrl = new URL('../assets/csv/avocado_sorted.csv', import.meta.url).href;
            d3.csv(csvUrl).then((data) => {
                data.forEach(d => {
                    d.Date = d3.timeParse("%Y-%m-%d")(d.Date);
                    d['Total Volume'] = +d['Total Volume'];
                });

                const filteredData = selectedRegions.map(region => {
                    return {
                        region,
                        values: data.filter((d, i) => d.region === region && i % 2 === 0)
                    };
                });

                const x = d3.scaleTime()
                    .domain(d3.extent(data, d => d.Date))
                    .range([0, width]);

                const y = d3.scaleLinear()
                    .domain([0, 12000000])
                    .range([height, 0]);

                const color = d3.scaleOrdinal(d3.schemeCategory10)
                    .domain(selectedRegions);

                const line = d3.line()
                    .x(d => x(d.Date))
                    .y(d => y(d['Total Volume']));

                svg.append('g')
                    .attr('transform', `translate(0, ${height})`)
                    .call(d3.axisBottom(x).ticks(d3.timeYear.every(1)).tickFormat(d3.timeFormat("%Y")))
                    .call(g => g.selectAll(".tick line")
                        .clone()
                        .attr("y2", -height)
                        .attr("stroke-opacity", 0.1));

                svg.append('g')
                    .call(d3.axisLeft(y))
                    .call(g => g.selectAll(".tick line")
                        .clone()
                        .attr("x2", width)
                        .attr("stroke-opacity", 0.1));

                filteredData.forEach(regionData => {
                    svg.append('path')
                        .datum(regionData.values)
                        .attr('fill', 'none')
                        .attr('stroke', color(regionData.region))
                        .attr('stroke-width', 1.5)
                        .attr('d', line);
                });

                const legend = svg.append('g')
                    .attr('transform', `translate(0, ${height + 30})`);

                selectedRegions.forEach((region, i) => {
                    const legendRow = legend.append('g')
                        .attr('transform', `translate(${i * 150}, 0)`);

                    legendRow.append('rect')
                        .attr('width', 10)
                        .attr('height', 10)
                        .attr('fill', color(region));

                    legendRow.append('text')
                        .attr('x', 20)
                        .attr('y', 10)
                        .attr('text-anchor', 'start')
                        .text(region);
                });
            })
            .catch(error => {
                console.error('Error loading or parsing data:', error);
            });

            gsap.to('.answerText', {
                scrollTrigger: {
                    trigger: '.intro-text',
                    start: 'bottom 0%',
                },
                opacity: 1,
                x: 0,
                duration: 0.75,
            });
            gsap.to('.chartContainer', {
                scrollTrigger: {
                    trigger: '.intro-text',
                    start: 'bottom 0%',
                },
                opacity: 1,
                x: 0,
                duration: 0.75,
            });
        });
        return { chartContainer };
    }
}
</script>

<template>
    <section class="volum-section section">
        <p class="intro-text">Datasetter er fra 2015 til 2018 og tar info fra forskjellige regioner i USA. 
            La oss se om vi klarer å merke trenden rundt den litt grovere broren til pæren på grafen som blir vist.
            Vi kan ta en titt på 3 forskjellige regioner.
        </p>

        <section>
            <p class="answerText">Ser ikke ut som der er noe særlig tegn på en oppover trend av avokadosalg. Dette kan lett forklares som at folk bare snakker om det mer, men faktisk ikke kjøper.</p>

            <div ref="chartContainer" class="chartContainer"></div>
        </section>
    </section>
</template>

<style lang="scss" scoped>
    .volum-section {
        display: flex;
        flex-direction: column;
        gap: 175px;

        .intro-text {
            font-size: 2rem;
            width: 40ch;
            margin: 100px 125px;
        }

        section {
            width: 100%;

            display: flex;
            gap: 20px;
            justify-content: center;

            p {
                transform: translateX(-200%);
                opacity: 0;
                width: 12ch;
                font-size: 2.25rem;
                text-wrap: wrap;
                white-space: pre-wrap;
            }
            
            .chartContainer {
                opacity: 0;
                transform: translateX(50%);
                width: 1200px;
                height: 600px;
            }
        }
    }
</style>