<script>
import { ref, onMounted } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";
import { gsap } from 'gsap';
import { ScrollTrigger } from "gsap/dist/ScrollTrigger"; 

export default {
    setup() {
        gsap.registerPlugin(ScrollTrigger);
        const cchartContainer = ref(null);
        const selectedRegions = ['California', 'NewYork', 'Tampa'];

        onMounted(() => {
            const margin = { top: 20, right: 30, bottom: 50, left: 70 };
            const width = cchartContainer.value.clientWidth - margin.left - margin.right;
            const height = cchartContainer.value.clientHeight - margin.top - margin.bottom;

            const svg = d3.select(cchartContainer.value)
                .append('svg')
                .attr('width', width + margin.left + margin.right)
                .attr('height', height + margin.top + margin.bottom)
                .append('g')
                .attr('transform', `translate(${margin.left}, ${margin.top})`);

            const csvUrl = new URL('../assets/csv/avocado_sorted.csv', import.meta.url).href;
            
            d3.csv(csvUrl).then((data) => {
                data.forEach(d => {
                    d.Date = d3.timeParse("%Y-%m-%d")(d.Date);
                    d['AveragePrice'] = +d['AveragePrice'];
                });

                const filteredData = selectedRegions.map(region => {
                    return {
                        region,
                        values: data.filter((d, i) => d.region === region && i % 2 === 0)
                    };
                });

                const color = d3.scaleOrdinal(d3.schemeCategory10)
                .domain(selectedRegions);

                const x = d3.scaleTime()
                    .domain(d3.extent(data, d => d.Date))
                    .range([0, width]);

                svg.append('g')
                    .attr('transform', `translate(0, ${height})`)
                    .call(d3.axisBottom(x).ticks(d3.timeYear.every(1)).tickFormat(d3.timeFormat("%Y")))
                    .call(g => g.selectAll(".tick line")
                        .clone()
                        .attr("y2", -height)
                        .attr("stroke-opacity", 0.1));

                const y = d3.scaleLinear()
                    .domain([0.5, 2])
                    .range([height, 0]);

                svg.append('g')
                    .call(d3.axisLeft(y))
                    .call(g => g.selectAll(".tick line")
                        .clone()
                        .attr("x2", width)
                        .attr("stroke-opacity", 0.1));

                const line = d3.line()
                    .x(d => x(d.Date))
                    .y(d => y(d['AveragePrice']));

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
            });

            gsap.to('.aanswerText', {
                scrollTrigger: {
                    trigger: '.intro-text',
                    start: 'bottom -130%',
                },
                opacity: 1,
                x: 0,
                duration: 0.75,
            });
            gsap.to('.cchartContainer', {
                scrollTrigger: {
                    trigger: '.intro-text',
                    start: 'bottom -130%',
                },
                opacity: 1,
                x: 0,
                duration: 0.75,
            });
        });
        return { cchartContainer };
    }
}
</script>

<template>
    <section class="price-section section">
        <p class="intro-text">La oss se om pris har forandret seg da
        </p>

        <section>
            <p class="aanswerText">Ser ut som avokadoprisene steg en smule før 2017, men har gått litt ned (men fortsatt over), det det var på starten av grafen. Kan sikkert forklares pga inflasjon eller noe sånt.</p>

            <div ref="cchartContainer" class="cchartContainer"></div>
        </section>
    </section>
</template>

<style lang="scss" scoped>
    .price-section {
        display: flex;
        flex-direction: column;
        gap: 175px;

        .intro-text {
            font-size: 2rem;
            width: 40ch;
            margin: 325px 125px 200px;
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
            
            .cchartContainer {
                opacity: 0;
                transform: translateX(50%);
                width: 1200px;
                height: 600px;
            }
        }
    }
</style>