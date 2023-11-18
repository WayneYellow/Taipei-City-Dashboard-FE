<!-- Developed by Taipei Urban Intelligence Center 2023 -->

<script setup>
import { ref } from "vue";

const props = defineProps(["chart_config", "activeChart", "series"]);

const chartOptions = ref({
	chart: {
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
		show: props.series.length > 1 ? true : false,
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
		show: true,
		width: 2,
	},
	tooltip: {
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			return '<div class="chart-tooltip">' +
				'<h6>' + w.globals.labels[dataPointIndex] + `${props.chart_config.categories ? '-' + w.globals.seriesNames[seriesIndex] : ''}` + '</h6>' +
				'<span>' + series[seriesIndex][dataPointIndex] + ` ${props.chart_config.unit}` + '</span>' +
				'</div>';
		},
	},
	xaxis: {
		axisBorder: {
			color: "#555",
			height: "0.8",
		},
		axisTicks: {
			show: false,
		},
		crosshairs: {
			show: false,
		},
		tooltip: {
			enabled: false,
		},
		type: "datetime",
	},
});


</script>

<template>
	<div v-if="activeChart === 'test'">
		<apexchart
			width="100%"
			height="260px"
			type="line"
			:options="chartOptions"
			:series="series"
		></apexchart>
	</div>
</template>
