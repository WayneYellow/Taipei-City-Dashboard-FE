<!-- Developed by Taipei Urban Intelligence Center 2023 -->

<script setup>
import { ref } from "vue";

import { useMapStore } from '../../store/mapStore';

const props = defineProps(['chart_config', 'activeChart', 'series', 'map_config']);
const mapStore = useMapStore();


const chartOptions = ref({
	chart: {
		stacked: true,
		toolbar: {
			show: false,
			tools: {
				zoom: false,
			},
		},
	},
	colors: props.chart_config.color,
	dataLabels: {
		enabled: false,
	},
	grid: {
		show: false,
	},
	legend: {
		// show: props.series.length > 1 ? true : false,
		show: props.chart_config.categories ? true : false,
	},
	markers: {
		hover: {
			size: 5,
		},
		size: 3,
		strokeWidth: 0,
	},
	stroke: {
		colors: props.chart_config.color,
		curve: "smooth",
		// curve: "straight",
		show: true,
		width: 2,
	},
	tooltip: {
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			return '<div class="chart-tooltip">' +
				'<h6>' + w.config.series[seriesIndex].data[dataPointIndex].x + `${props.chart_config.categories ? '-' + w.globals.seriesNames[seriesIndex] : ''}` + '</h6>' +
				'<span>' + series[seriesIndex][dataPointIndex] + ` ${props.chart_config.unit}` + '</span>' +
				'</div>';
		},
		// custom: function ({ series, seriesIndex, dataPointIndex, w }) {
		// 	// The class "chart-tooltip" could be edited in /assets/styles/chartStyles.css
		// 	return (
		// 		'<div class="chart-tooltip">' +
		// 		"<h6>" +
		// 		`${parseTime(
		// 			w.config.series[seriesIndex].data[dataPointIndex].x
		// 		)}` +
		// 		` - ${w.globals.seriesNames[seriesIndex]}` +
		// 		"</h6>" +
		// 		"<span>" +
		// 		series[seriesIndex][dataPointIndex] +
		// 		` ${props.chart_config.unit}` +
		// 		"</span>" +
		// 		"</div>"
		// 	);
		// },
	},
	xaxis: {
		// axisBorder: {
		// 	color: "#555",
		// 	height: "0.8",
		// },
		// axisTicks: {
		// 	show: false,
		// },
		// crosshairs: {// 虛線
		// 	show: false,
		// },
		tooltip: {
			enabled: false,
		},
		// type: "datetime",

		// axisBorder: {// X軸的線
		// 	show: false,
		// },
		// axisTicks: {// X軸的刻度
		// 	show: false,
		// },
		// labels: {
		// 	show: false,
		// },
		categories: props.chart_config.categories ? props.chart_config.categories : [],
		labels: {
			offsetY: 5,
		},
		type: 'category',
	},
});

// function parseTime(time) {
// 	return time.replace("T", " ").replace("+08:00", " ");
// }

const selectedIndex = ref(null);

function handleDataSelection(e, chartContext, config) {
	if (!props.chart_config.map_filter) {
		return;
	}
	const toFilter = config.dataPointIndex;
	if (toFilter !== selectedIndex.value) {
		mapStore.addLayerFilter(`${props.map_config[0].index}-${props.map_config[0].type}`, props.chart_config.map_filter[0], props.chart_config.map_filter[1][toFilter]);
		selectedIndex.value = toFilter;
	} else {
		mapStore.clearLayerFilter(`${props.map_config[0].index}-${props.map_config[0].type}`);
		selectedIndex.value = null;
	}
}

</script>

<template>
	<div v-if="activeChart === 'LineChart'">
		<apexchart
			width="100%"
			height="260px"
			type="line"
			:options="chartOptions"
			:series="series"
			@dataPointSelection="handleDataSelection"
		></apexchart>
	</div>
</template>
