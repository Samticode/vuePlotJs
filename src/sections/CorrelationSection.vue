<script>
import { ref, onMounted } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";
import { gsap } from 'gsap';
import { ScrollTrigger } from "gsap/dist/ScrollTrigger"; 

export default {
    setup() {
        gsap.registerPlugin(ScrollTrigger);
        const ccchartContainer = ref(null);
        const selectedRegions = ['California', 'NewYork', 'Tampa', 'Atlanta', 'LasVegas' ];
        const selectedMonths = ['Jan', 'Feb', 'March', 'April', 'May', 'June', 'July', 'Aug', 'Sept', 'Oct', 'Nov', 'Dec'];

        const generatePlaceholderData = () => {
            const regions = ['California', 'NewYork', 'Tampa', 'Atlanta', 'LasVegas'];
            const data = [];
            const startDate = new Date(2022, 0, 1); // Start from January 2022

            for (let i = 0; i < 50; i++) {
                const region = regions[i % regions.length];
                const date = new Date(startDate);
                date.setMonth(Math.floor(Math.random() * 12));
                const totalVolume = Math.floor(Math.random() * 1000) + 1;

                data.push({
                    region,
                    Date: date,
                    TotalVolume: totalVolume
                });
            }

            return data;
        };

        onMounted(() => {
            const margin = { top: 20, right: 30, bottom: 60, left: 70 };
            const width = ccchartContainer.value.clientWidth - margin.left - margin.right;
            const height = ccchartContainer.value.clientHeight - margin.top - margin.bottom;
            const placeholderData = generatePlaceholderData();


            const svg = d3.select(ccchartContainer.value)
                .append('svg')
                .attr('width', width + margin.left + margin.right)
                .attr('height', height + margin.top + margin.bottom)
                .append('g')
                .attr('transform', `translate(${margin.left}, ${margin.top})`);
            
            // const x = d3.scaleBand()
            // .domain(placeholderData.map(d => d.Date))
            // .range([0, width])
            // .padding(0.01);

            // svg.append('g')
            //     .attr('transform', `translate(0, ${height})`)
            //     .call(d3.axisBottom(x).tickFormat(d3.timeFormat("%b")))
            //     .selectAll('text')
            //     .style('text-anchor', 'end')
            //     .attr('dx', '-.8em')
            //     .attr('dy', '.15em')
            //     .style('font-size', '14px')
            //     .style('font-weight', '500')
            //     .attr('transform', 'rotate(-65)');

            // const y = d3.scaleBand()
            //     .domain(selectedRegions)
            //     .range([0, height])
            //     .padding(0.1);

            // svg.append('g')
            //     .call(d3.axisLeft(y))
            //     .selectAll('text')
            //     .style('font-size', '14px')
            //     .style('font-weight', '500')

            // svg.selectAll()
            //     .data(placeholderData)
            //     .enter()
            //     .append('rect')
            //     .attr('x', d => x(d.Date))
            //     .attr('y', d => y(d.region))
            //     .attr('width', x.bandwidth())
            //     .attr('height', y.bandwidth())
            //     .style('fill', 'steelblue')
            //     .style('opacity', 0.7);

            const csvUrl = new URL('../assets/csv/avocado_sorted.csv', import.meta.url).href;
            d3.csv(csvUrl).then((data) => {
                data.forEach(d => {
                    d.Date = new Date(d.Date);
                    d.TotalVolume = +d['Total Volume'];
                });

                const filteredData = data.filter(d => selectedRegions.includes(d.region));

                const x = d3.scaleBand()
                    .domain(selectedMonths)
                    .range([0, width])
                    .padding(0.05);

                svg.append('g')
                    .attr('transform', `translate(0, ${height})`)
                    .call(d3.axisBottom(x))
                    .selectAll('text')
                    .style('text-anchor', 'end')
                    .attr('dx', '-.8em')
                    .attr('dy', '.15em')
                    .style('font-size', '14px')
                    .style('font-weight', '500')
                    .attr('transform', 'rotate(-65)');

                const y = d3.scaleBand()
                    .domain(selectedRegions)
                    .range([0, height])
                    .padding(0.05);

                svg.append('g')
                    .call(d3.axisLeft(y))
                    .selectAll('text')
                    .style('font-size', '14px')
                    .style('font-weight', '500');

                const colorScale = d3.scaleSequential(d3.interpolateBlues)
                    .domain([0, d3.max(filteredData, d => d.TotalVolume)]);

                svg.selectAll()
                    .data(filteredData)
                    .enter()
                    .append('rect')
                    .attr('x', d => x(selectedMonths[d.Date.getMonth()]))
                    .attr('y', d => y(d.region))
                    .attr('width', x.bandwidth())
                    .attr('height', y.bandwidth())
                    .style('fill', d => colorScale(d.TotalVolume))
                    .style('opacity', 0.2)
                    .on('mouseover', function(event, d) {
                        d3.select(this)
                            .style('fill', 'orange');
                        tooltip.transition()
                            .duration(200)
                            .style('opacity', .9);
                        tooltip.html(`Total Volume: ${d.TotalVolume}`)
                            .style('left', (event.pageX + 5) + 'px')
                            .style('top', (event.pageY - 28) + 'px');
                    })
                    .on('mouseout', function(d) {
                        d3.select(this)
                            .style('fill', d => colorScale(d.TotalVolume));
                        tooltip.transition()
                            .duration(500)
                            .style('opacity', 0);
                    });

                const tooltip = d3.select('body').append('div')
                    .attr('class', 'tooltip')
                    .style('position', 'absolute')
                    .style('text-align', 'center')
                    .style('width', '120px')
                    .style('height', '28px')
                    .style('padding', '2px')
                    .style('font', '12px sans-serif')
                    .style('background', 'lightsteelblue')
                    .style('border', '0px')
                    .style('border-radius', '8px')
                    .style('pointer-events', 'none')
                    .style('opacity', 0);


            });

            gsap.to('.aaanswerText', {
                scrollTrigger: {
                    trigger: '.intro-text',
                    start: 'bottom -260%',
                },
                opacity: 1,
                x: 0,
                duration: 0.75,
            });
            gsap.to('.ccchartContainer', {
                scrollTrigger: {
                    trigger: '.intro-text',
                    start: 'bottom -260%',
                },
                opacity: 1,
                x: 0,
                duration: 0.75,
            });
        });
        return { ccchartContainer };
    }
}
</script>

<template>
    <section class="price-section section">
        <p class="intro-text">Mmm. Kan vi sesongmessige trender?
        </p>

        <section>
            <p class="aaanswerText">ufaluv</p>

            <div ref="ccchartContainer" class="ccchartContainer"></div>
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
            padding-bottom: 110px;

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
            
            .ccchartContainer {
                opacity: 0;
                transform: translateX(50%);
                width: 1200px;
                height: 550px;
            }
        }
    }
</style>