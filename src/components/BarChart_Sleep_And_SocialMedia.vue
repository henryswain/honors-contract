<script setup>
    // get basics from chart.js
    import { Chart } from 'chart.js/auto';

    import { onMounted, onUpdated, ref, nextTick, computed} from 'vue';

    const screenSize = ref("")
    window.addEventListener('resize', handleScreenChange)


function handleScreenChange() {
    if (window.innerWidth <= 480) {
        screenSize.value = "x-small"
    }
    else if (window.innerWidth >= 481 && window.innerWidth <= 768) {
        screenSize.value = "small";
    } else if (window.innerWidth > 768 && window.innerWidth <= 1279) {
        screenSize.value = "medium";
    } else {
        screenSize.value = "large";
    }
    
    nextTick();
    
    // Update chart if it exists
    if (chartObj) {
        // Update font sizes
        console.log("screenSize.value: ", screenSize.value)
        chartObj.options.scales.y.title.font.size = (screenSize.value === "x-small") ? 12 : ((screenSize.value === "small") ? 16 : (screenSize.value === "medium" ? 20 :24));
        chartObj.options.scales.y.title.text = (screenSize.value == "large") ? "percentage or partipants" : ["percentage of ", "participants"]
        chartObj.options.scales.x.title.font.size = (screenSize.value === "x-small") ? 12 : ((screenSize.value === "small") ? 16 : (screenSize.value === "medium" ? 24 : 28));
        chartObj.options.scales.x.ticks.font.size = (screenSize.value === "x-small") ? 8 : ((screenSize.value === "small") ? 12 : (screenSize.value === "medium" ? 16 : 20));
        chartObj.options.plugins.title.font.size = (screenSize.value === "x-small") ? 12 : ((screenSize.value === "small") ? 16 : (screenSize.value === "medium" ? 24 : 28));
        
        // Re-render the chart
        chartObj.update();
    }
}


    // define the data coming into the component
    const props = defineProps({
        labels: Array, // x-axis labels
        datasets: Array, // data to be displayed
        chartLabel: String, // chart label
        amount: String

    });

    // holds chart object
    let chartObj;

    async function createChart() {
        console.log("createChart called")
        // creates chart based on props
        // see full docs here: https://www.chartjs.org/docs/latest/charts/line.html
        chartObj = new Chart(
            // 'thisChart' matches id of div in template
            document.getElementById('thisChart'),
            {
                type: 'bar',
                options: {
                    animation: {
                        duration: 0 // Disables animation
                    },
                    scales: {
                        y: {
                            title: {
                                display: true,
                                text: (screenSize.value == "small" || screenSize.value == "x-small") ? ["percentage of ", "participants"] : "percentage of participants",
                                font: {
                                    size: 24
                                }
                            },
                            min: 0,
                            max: 100
                        },
                        x: {
                            title: {
                                display: true,
                                text: `${props.chartLabel} categories of participants with ${props.amount}`,
                                font: {
                                    size: 24
                                    // size: (screenSize.value == "small") ? 9 : ((screenSize.value == "medum") ? 16 : 24)
                                }
                            },
                            ticks: {
                                autoSkip: false,
                                // maxRotation: ((screenSize.value == "x-small") ? 90 : undefined),
                                // minRotation: ((screenSize.value == "small" || screenSize.value == "x-small") ? 50 : undefined),
                                font: {
                                    size: 12
                                    // size: (screenSize.value == "small") ? 4 : ((screenSize.value == "medum") ? 6 : 12)
                                }
                            }
                        },
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
                        tooltip: {
                            callbacks: {
                                title: (context) => {
                                    return context[0].label.replaceAll(",", " ")
                                }
                            }
                        },
                        legend: {
                            display: false
                        },
                        title: {
                            display: true,
                            text: `Amount of ${props.chartLabel} when using social media for ${props.amount}`,
                            font: {
                                size: 40
                                // size: (screenSize.value == "small") ? 20 : ((screenSize.value == "medum") ? 30 : 40)
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
            // Initial check
        handleScreenChange();
        // calls asynchronous function to create chart
        createChart();
    }        
    );

    // updates chart when props change
    async function updateChart() {
        // updates data for chart and re-renders
        chartObj.data.datasets = props.datasets;
        chartObj.options.scales.x.title.text = `${props.chartLabel} categories of participants with ${props.amount}`;
        chartObj.options.plugins.title.text = `Amount of ${props.chartLabel} when using social media for ${props.amount}`;
        chartObj.update();
    }

    // updates chart when props are updated
    onUpdated(() => {
        // calls asynchronous function to update chart
        updateChart();
    }        
    )

</script>

<style>
.chart-container {
  position: relative;
  margin: auto;
  height: 80vh;
  width: 80vw;
  max-width: 100%;
}
</style>
<template>
    <!-- <div class="container"> -->
        <!-- matches id used in createChart() -->

        <div class="chart-container">
            <canvas id="thisChart"></canvas>
        </div>
    <!-- </div> -->
</template>