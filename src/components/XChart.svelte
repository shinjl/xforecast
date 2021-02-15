<script lang="ts">
    import { afterUpdate, onMount } from 'svelte';

    // Craete random ID for chart container
    const chartContainerId = `chart-container-${Math.floor(Math.random() * 1000000)}`;

    export let data = [];
    export let title = '';
    export let timezone = 'UTC';
    export let tooltipFormat = '%m/%d %H:%M';
    export let labelFormatMonth = '%m/%d';
    export let labelFormatDay = '%m/%d';
    export let labelFormatHour = '%H:%M';

    let charts;

    // Highcharts options
    const chartOptions = {
        chart: {
            type: 'spline',
            height: '250px',
        },
        time: {
            timezone,
            useUTC: false,
        },
        title: {
            text: title,
        },
        subtitle: {
            text: null,
        },
        legend: {
            enabled: false,
        },
        credits: {
            enabled: false,
        },
        xAxis: {
            type: 'datetime',
            dateTimeLabelFormats: {
                hour: labelFormatHour,
                day: labelFormatDay,
                month: labelFormatMonth,
            },
            title: {
                text: null,
            },
        },
        yAxis: {
            title: {
                text: null,
            },
            labels: {
                enabled: false,
            },
            gridLineWidth: 1,
        },
        tooltip: {
            headerFormat: '',
            pointFormat: `{point.x: ${tooltipFormat}}: {point.y}&deg;`,
            outside: true,
        },
        plotOptions: {
            series: {
                marker: {
                    enabled: true,
                },
                dataLabels: {
                    enabled: true,
                    y: -15,
                    useHTML: true,
                    formatter: function () {
                        return `${this.y}&deg;`;
                    },
                },
                states: {
                    hover: {
                        enabled: false,
                    },
                },
                enableMouseTracking: true,
                stickyTracking: false,
                lineWidth: 1,
            },
        },
        series: [
            {
                name: null,
                data: [],
            },
        ],
    };

    /**
     * Create chart instance
     */
    const createHighCharts = () => {
        charts = Highcharts.chart(chartContainerId, chartOptions);
    };

    /**
     * Update charts
     */
    const updateChart = () => {
        let chartData = [];
        data.forEach(({ x, y, icon }) => {
            chartData.push({
                x,
                y,
                marker: {
                    symbol: icon,
                    width: 48,
                    height: 48,
                },
            });
        });
        charts.series[0].setData(chartData);
        charts.redraw(true);
    };

    onMount(createHighCharts);
    afterUpdate(updateChart);
</script>

<div id={chartContainerId} class="chart" />

<style>
    .chart {
        box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
        background: #ffffff;
        padding: 1rem 0.5rem;
    }
</style>
