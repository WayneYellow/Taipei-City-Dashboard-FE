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
            minBubbleRadius: 10,
            maxBubbleRadius: 30,
    }
	},
	dataLabels:{
        enabled:false
    },
	// The class "chart-tooltip" could be edited in /assets/styles/chartStyles.css
	tooltip: {
		custom: function ({ series, seriesIndex, dataPointIndex, w }) {
			return (
              '<div class="chart-tooltip">'  +
                '<h6>太陽能裝置容量: '+ w.config.series[seriesIndex].data[dataPointIndex].z+' 十萬瓦</h6>'+
              '<span>' + w.config.series[seriesIndex].name + '</span>'+
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
        title:{
            text: "平方公尺",
            style:{
                color: "#4CAF50", 
            }
        }
	},
});
</script>

<template>
	<div v-if="activeChart === 'BubbleChart'">
		<apexchart width="100%" :height=250 type="bubble" :options="chartOptions" :series="series"></apexchart>
	</div>
</template>