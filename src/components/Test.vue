<script>
import { ref, onMounted } from 'vue';
import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm";

export default {
    setup() {
        const chartContainer = ref(null);

        onMounted(() => {
            const svg = d3.select(chartContainer.value)
                .append("svg")
                .attr("width", 400)
                .attr("height", 400);

            const circle = svg.append("circle")
                .attr("cx", 50)
                .attr("cy", 50)
                .attr("r", 20)
                .attr("fill", "blue");

            circle.transition()
                .duration(2000)
                .attr("cx", 200)
                .attr("cy", 200)
                .attr("r", 50);



            const simplePlotSvg = d3.select(chartContainer.value)
                .append("svg")
                .attr("width", 400)
                .attr("height", 400);

            const data = [10, 20, 30, 40];
            const rects = simplePlotSvg.selectAll("rect")
                .data(data);

            rects.enter().append("rect")
                .attr("x", (d, i) => i * 105)
                .attr("y", d => 200 - d)
                .attr("width", 50)
                .attr("height", d => d)
                .attr("fill", "blue");



            const threeDSvg = d3.select(chartContainer.value)
                .append("svg")
                .attr("width", 400)
                .attr("height", 400);

            
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
    display: flex;
    flex-direction: row;
    gap: 20px;
}


</style>
