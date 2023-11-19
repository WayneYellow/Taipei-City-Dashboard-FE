<!-- Developed by Taipei Urban Intelligence Center 2023 -->

<script setup>
import { ref } from 'vue';
import { useMapStore } from '../../store/mapStore';

const props = defineProps(['chart_config', 'activeChart', 'series', 'map_config']);
const mapStore = useMapStore();

const chartOptions = ref({
	chart: {
		stacked: true,
		toolbar: {
			show: false
		},
	},
	colors: props.chart_config.color,
	dataLabels: {
		enabled: false,
		//enabled: props.chart_config.categories ? false : true,
		offsetY: 20,
	},
	grid: {
		show: false,
	},
	legend: {
		show: props.chart_config.categories ? true : false,

	},
	plotOptions: {
		bar: {
			borderRadius: 5,

		},
	},
	stroke: {
		colors: ['#0f0', '#f00'],
		show: true,
		width: 2,
	},
	tooltip: {
		// The class "chart-tooltip" could be edited in /assets/styles/chartStyles.css
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			return '<div class="chart-tooltip">' +
				'<h6>' + w.globals.seriesNames[seriesIndex] + '</h6>' +
				'<span>' + series[seriesIndex][dataPointIndex] + ` ${props.chart_config.unit}` + '</span>' +
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
			offsetY: 0,
		},
		type: 'category',
		tooltip:{
			enabled: false
		}
	},
});

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
	<div v-if="activeChart === 'mixed_chart'">
		<apexchart width="100%" height="270px" :options="chartOptions" :series="series"
			@dataPointSelection="handleDataSelection"></apexchart>
	</div>
</template>