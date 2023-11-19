<!-- Developed by Taipei Urban Intelligence Center 2023 -->

<script setup>
import { computed, ref } from 'vue';
import { useMapStore } from '../../store/mapStore';

const props = defineProps(['chart_config', 'activeChart', 'series', 'map_config']);

const mapStore = useMapStore();

const heatmapData = computed(() => {
	let output = {};
	let highest = 0;
	let sum = 0;
	if (props.series.length === 1) {
		props.series[0].data.forEach((item) => {
			output[item.x] = item.y;
			if (item.y > highest) {
				highest = item.y;
			}
			sum += item.y;
		});
	} else {
		props.series.forEach((serie) => {
			for (let i = 0; i < props.chart_config.categories.length; i++) {
				if (!output[props.chart_config.categories[i]]) {
					output[props.chart_config.categories[i]] = 0;
				}
				output[props.chart_config.categories[i]] += +serie.data[i];

				if (+serie.data[i] > highest) highest = +serie.data[i];
			}
		});
		sum = Object.values(output).reduce((partialSum, a) => partialSum + a, 0);
	}

	output.highest = highest;
	output.sum = sum;
	return output;
});

// const colorScale = computed(() => {
// 	const ranges = props.chart_config.color.map((el, index) => (
// 		{
// 			to: Math.floor((heatmapData.value.highest / props.chart_config.color.length) * (props.chart_config.color.length - index)),
// 			from: Math.floor((heatmapData.value.highest / props.chart_config.color.length) * (props.chart_config.color.length - index - 1)) + 1,
// 			color: el
// 		}
// 	)
// 	);
// 	ranges.unshift({
// 		to: 0,
// 		from: 0,
// 		color: "#444444"
// 	});
// 	return ranges;
// });

const chartOptions = ref({
	chart: {
		stacked: true,
		toolbar: {
			show: false,
		},
	},
	dataLabels: {
		distributed: false,
		style: {
			fontSize: '12px',
			fontWeight: 'normal'
		}
	},
	grid: {
		show: false,
	},
	legend: {
		show: false,
	},
	markers: {
		size: 3,
		strokeWidth: 0,
	},
	plotOptions: {
		heatmap: {
			distributed: true,
			enableShades: false,
			radius: 4,
			colorScale: {
				ranges: [{ from: 0, to: 5, color: "#001F00", name: "l1" },{ from: 5, to: 10, color: "#0B2E00", name: "l2" },{ from: 10, to: 15, color: "#163D00", name: "l3" },{ from: 15, to: 20, color: "#215C00", name: "l4" },{ from: 20, to: 25, color: "#2C6B00", name: "l5" },{ from: 25, to: 30, color: "#377A00", name: "l6" },{ from: 30, to: 35, color: "#428900", name: "l7" },{ from: 35, to: 40, color: "#4D9800", name: "l8" },{ from: 40, to: 45, color: "#58A700", name: "l9" },{ from: 45, to: 50, color: "#00A100", name: "l10" },{ from: 50, to: 55, color: "#001836", name: "m1" },{ from: 55, to: 60, color: "#1f2c4c", name: "m2" },{ from: 60, to: 65, color: "#3d4283", name: "m3" },{ from: 65, to: 70, color: "#5b5abf", name: "m4" },{ from: 70, to: 75, color: "#7a83f8", name: "m5" },{ from: 75, to: 80, color: "#8fb2ff", name: "m6" },{ from: 80, to: 85, color: "#a5e0ff", name: "m7" },{ from: 85, to: 90, color: "#bbffff", name: "m8" },{ from: 90, to: 95, color: "#d2ffff", name: "m9" },{ from: 95, to: 100, color: "#e9ffFF", name: "m10" },{ from: 100, to: 115, color: "#241900", name: "h1" },{ from: 115, to: 130, color: "#4D2E00", name: "h2" },{ from: 130, to: 145, color: "#784300", name: "h3" },{ from: 145, to: 160, color: "#A65C00", name: "h4" },{ from: 160, to: 175, color: "#D97400", name: "h5" },{ from: 175, to: 190, color: "#FF8D00", name: "h6" },{ from: 190, to: 205, color: "#FFA600", name: "h7" },{ from: 205, to: 220, color: "#FFBF00", name: "h8" },{ from: 220, to: 235, color: "#FFD800", name: "h9" },{ from: 235, to: 250, color: "#FFF100", name: "h10" }
				],
			}
		},
	},
	stroke: {
		show: true,
		width: 2,
		colors: ['#282a2c'],
	},
	tooltip: {
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			// The class "chart-tooltip" could be edited in /assets/styles/chartStyles.css
			return '<div class="chart-tooltip">' +
				'<h6>' + `${w.globals.seriesNames[seriesIndex]}-${w.globals.labels[dataPointIndex]}` + '</h6>' +
				'<span>' + `${series[seriesIndex][dataPointIndex]}` + `${props.chart_config.unit}` + '</span>' +
				'</div>';
		},
	},
	xaxis: {
		axisBorder: {
			show: false,
		},
		axisTicks: {
			show: false,
		},
		categories: props.chart_config.categories ? props.chart_config.categories : [],
		labels: {
			offsetY: 5,
			formatter: function (value) {
				return value.length > 7 ? value.slice(0, 6) + "..." : value;
			}
		},
		tooltip: {
			enabled: false,
		},
		type: 'category',
	},
	yaxis: {
		max: function (max) {
			if (!props.chart_config.categories) {
				return max;
			}
			return heatmapData.value.highest;
		},
	}
});

const selectedIndex = ref(null);

function handleDataSelection(e, chartContext, config) {
	if (!props.chart_config.map_filter) {
		return;
	}
	let toFilter = `${config.dataPointIndex} -${config.seriesIndex}`;
	if (toFilter !== selectedIndex.value) {
		mapStore.addLayerFilter(`${props.map_config[0].index}-${props.map_config[0].type}`, props.chart_config.map_filter[0], props.chart_config.map_filter[1][config.dataPointIndex], props.map_config[0]);
		selectedIndex.value = toFilter;
	} else {
		mapStore.clearLayerFilter(`${props.map_config[0].index}-${props.map_config[0].type}`, props.map_config[0]);
		selectedIndex.value = null;
	}
}
</script>

<template>
	<div v-if="activeChart === 'HeatmapChart'" class="heatmapchart">
		<div class="heatmapchart-title">
			<h5>總合</h5>
			<h6>{{ heatmapData.sum }} {{ chart_config.unit }}</h6>
		</div>
		<apexchart width="100%" height="360px" type="heatmap" :options="chartOptions" :series="series"
			@dataPointSelection="handleDataSelection"></apexchart>
	</div>
</template>

<style scoped lang="scss">
.heatmapchart {

	&-title {
		display: flex;
		justify-content: center;
		flex-direction: column;
		margin: -0.2rem 0 -1.5rem;

		h5 {
			color: var(--color-complement-text);
		}

		h6 {
			color: var(--color-complement-text);
			font-size: var(--font-m);
			font-weight: 400;
		}
	}
}
</style>