<script setup>
import { ref, computed } from 'vue';
import { useMapStore } from '../../store/mapStore';

const props = defineProps(['chart_config', 'activeChart', 'series', 'map_config']);
const mapStore = useMapStore();

const chartOptions = ref({
	chart: {
		stacked: false,
		toolbar: {
			show: false
		},
	},
    legend: {
		show: props.series.length > 1 ? true : false,
	},
    fill: {
        opacity: 0.8
    },
	colors: props.chart_config.color,
	grid: {
		show: false,
	},
	plotOptions: {
		bubble: {
            zScaling: false,
            minBubbleRadius: undefined,
            maxBubbleRadius: undefined,
    }
	},
	dataLabels:{
        enabled:false
    },
	// The class "chart-tooltip" could be edited in /assets/styles/chartStyles.css
	tooltip: {
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			return (
              '<div class="chart-tooltip">' +
              '<h6>X: ' + w.config.series[seriesIndex].data[dataPointIndex].x +
                'Y:'+w.config.series[seriesIndex].data[dataPointIndex].y +  '</h6>' +
              '<span>Z: ' + w.config.series[seriesIndex].data[dataPointIndex].z + '</span>'+
              '</div>'
            );
		},
		followCursor: true,
	},
	xaxis: {
        tickAmount: 5,
        type: 'category',
		axisBorder: {
			show: false,
		},
		axisTicks: {
			show: false,
		},
		labels: {
			show: true,
		},
	},
});
</script>

<template>
	<div v-if="activeChart === 'BubbleChart'">
		<apexchart width="100%" :height=250 type="bubble" :options="chartOptions" :series="series"></apexchart>
	</div>
</template>