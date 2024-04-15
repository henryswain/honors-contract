<script setup>
    // get basics from chart.js
    import { Chart } from 'chart.js/auto';

    // we need onMounted and onUpdated from Vue
    import { onMounted, onUpdated } from 'vue';

    // define the data coming into the component
    const props = defineProps({
        labels: Array, // x-axis labels
        datasets: Array, // data to be displayed
        chartLabel: String, // chart label
        amountOfSocMediaUse: String
    });

    // holds chart object
    let chartObj;

    async function createChart() {
        // creates chart based on props
        // see full docs here: https://www.chartjs.org/docs/latest/charts/line.html
        chartObj = new Chart(
            // 'thisChart' matches id of div in template
            document.getElementById('thisChart'),
            {
                type: 'bar',
                options: {
                    scales: {
                        y: {
                            title: {
                                display: true,
                                text: "percentage of participants",
                                font: {
                                    size: 24
                                }
                            }
                        }
                    },
                    responsive: true,
                    tooltip: {
                        enabled: true,

                        callbacks: {
                            label: (context) => {
                                const index = context.dataIndex;
                                const customLabels = ["< 4 hours of sleep", "4 - 6 hours of sleep", "7 - 8 hours of sleep", "> 8 hours of sleep"]
                                return `${customLabels[index] || labels[index]}: ${context.dataset.data[index]}`
                            }
                        }
                    },
                    plugins: {
                        legend: {
                            display: false
                        },
                        title: {
                            display: true,
                            text: props.chartLabel,
                            font: {
                                size: 40
                            }
                        },
                        datalabels: {
                            enabled: true,
                            anchor: 'end', // Place labels on the right side of the bar
                            align: 'top',  // Align labels to the top of the bar
                 
                        }
                    }
                },
                data: {
                    labels: props.labels,
                    datasets: props.datasets
                }
            }
        );
    }

    // when the page loads, we create a chart
    onMounted(() => {
        // calls asynchronous function to create chart
        createChart();
    }        
    );

    // updates chart when props change
    async function updateChart() {
        // updates data for chart and re-renders
        chartObj.data.datasets = props.datasets;
        chartObj.update();
    }

    // updates chart when props are updated
    onUpdated(() => {
        // calls asynchronous function to update chart
        updateChart();
    }        
    );
</script>

<template>
    <div class="container">
        <!-- matches id used in createChart() -->
        <canvas id="thisChart"></canvas>
    </div>
</template>