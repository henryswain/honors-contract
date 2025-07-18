<script setup>
// to parse csv file
import papaparse from 'papaparse';


const labelsforSleep = ["< 4 hours of sleep", "4 - 6 hours of sleep", "7 - 8 hours of sleep", "> 8 hours of sleep"]
// bits of vue we are using
import { ref, onMounted, computed } from 'vue';

// import LineChart
import BarChart_Sleep_And_SocialMedia from './components/BarChart_Sleep_And_SocialMedia.vue';

// data imported
const amountOfSocMediaUse = ref('less than 1 hour')

// data for line chart
const chartData = ref([]);

// tells us if the data has been loaded
const loaded = ref(false);



// location of data

const dataFile = "./src/assets/Bangladesh social media mental health dataset.csv";

// asynchronously loads data when page is mounted
onMounted(() => fetch(dataFile)
  .then((res) => res.text())
  .then((text) => loadData(text))
  .then((parsedText) => showData(parsedText)));


function loadFileWithUpdatedVar(SocMedUse) {
  amountOfSocMediaUse.value = SocMedUse;
  loaded.value = false
  fetch(dataFile)
  .then((res) => res.text())
  .then((text) => loadData(text))
  .then((parsedText) => showData(parsedText));
}

// asynchronous function to wrap around parse process
async function loadData(dataText) { 
  console.log("ready to load data");
  let data = papaparse.parse(dataText, {delimter: ",", header: true});
  return data;
}

// get data ready to show
function showData(parsedData) {
  console.log("data loaded");
  let freqTimeOnSocMediaWithDup = [];
  let amountOfSleepWithDup = []
  for (let i of parsedData.data) {
    freqTimeOnSocMediaWithDup.push(i[['7. How much time do you spend daily in social media?']])
    amountOfSleepWithDup.push(i['4. How many hours of actual sleep do you get at night?'])
  }

  let numberOfRows = []
  for (let i = 0; i < freqTimeOnSocMediaWithDup.length; i++) {
    numberOfRows.push(i);
  }

// creates an object with the counts of each amount of time on social media with sleep less than 4 hours
  if (amountOfSocMediaUse.value == 'less than 1 hour') {

    let countsForSleepWithSocLessThan1 = {"<4": 0, "4-6": 0, "7-8": 0, ">8": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "Less than 1 hour") {
        if (amountOfSleepWithDup[i] === "Less than 4 hours") {
          countsForSleepWithSocLessThan1["<4"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "4-6 hours") {
          countsForSleepWithSocLessThan1["4-6"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "7-8 hours") {
          countsForSleepWithSocLessThan1["7-8"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "More than 8 hours") {
          countsForSleepWithSocLessThan1[">8"] += 1;
        }
      }
    }


    let countsForSleepWithSocLessThan1AsPercentage = []

    // loads each count into a variable
    let percentageLessThan1LessThan4 = countsForSleepWithSocLessThan1["<4"] / (countsForSleepWithSocLessThan1["<4"] + countsForSleepWithSocLessThan1["4-6"] + countsForSleepWithSocLessThan1["7-8"] + countsForSleepWithSocLessThan1[">8"]) * 100;
    let percentageLessThan14to6 = countsForSleepWithSocLessThan1["4-6"] / (countsForSleepWithSocLessThan1["<4"] + countsForSleepWithSocLessThan1["4-6"] + countsForSleepWithSocLessThan1["7-8"] + countsForSleepWithSocLessThan1[">8"]) * 100;
    let percentageLessThan17to8 = countsForSleepWithSocLessThan1["7-8"] / (countsForSleepWithSocLessThan1["<4"] + countsForSleepWithSocLessThan1["4-6"] + countsForSleepWithSocLessThan1["7-8"] + countsForSleepWithSocLessThan1[">8"]) * 100;
    let percentageLessThan1MoreThan8 = countsForSleepWithSocLessThan1[">8"] / (countsForSleepWithSocLessThan1["<4"] + countsForSleepWithSocLessThan1["4-6"] + countsForSleepWithSocLessThan1["7-8"] + countsForSleepWithSocLessThan1[">8"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForSleepWithSocLessThan1AsPercentage.push(percentageLessThan1LessThan4)
    countsForSleepWithSocLessThan1AsPercentage.push(percentageLessThan14to6)
    countsForSleepWithSocLessThan1AsPercentage.push(percentageLessThan17to8)
    countsForSleepWithSocLessThan1AsPercentage.push(percentageLessThan1MoreThan8)

    
    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForSleepWithSocLessThan1AsPercentage,
                        backgroundColor: colors}]
    // show data

    console.log("chartData.value " + JSON.stringify(chartData.value))
    loaded.value = true;
  }

  else if (amountOfSocMediaUse.value == "1 - 3 hours") {


    let countsForSleepWithSoc1to3 = {"<4": 0, "4-6": 0, "7-8": 0, ">8": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "1-3 hours") {
        if (amountOfSleepWithDup[i] === "Less than 4 hours") {
          countsForSleepWithSoc1to3["<4"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "4-6 hours") {
          countsForSleepWithSoc1to3["4-6"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "7-8 hours") {
          countsForSleepWithSoc1to3["7-8"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "More than 8 hours") {
          countsForSleepWithSoc1to3[">8"] += 1;
        }
      }
    }


    let countsForSleepWithSoc1to3AsPercentage = []

    // loads each count into a variable
    let percentage1to3LessThan4 = countsForSleepWithSoc1to3["<4"] / (countsForSleepWithSoc1to3["<4"] +countsForSleepWithSoc1to3["4-6"] + countsForSleepWithSoc1to3["7-8"] + countsForSleepWithSoc1to3[">8"]) * 100;
    let percentage1to34to6 = countsForSleepWithSoc1to3["4-6"] / (countsForSleepWithSoc1to3["<4"] + countsForSleepWithSoc1to3["4-6"] + countsForSleepWithSoc1to3["7-8"] + countsForSleepWithSoc1to3[">8"]) * 100;
    let percentage1to37to8 = countsForSleepWithSoc1to3["7-8"] / (countsForSleepWithSoc1to3["<4"] + countsForSleepWithSoc1to3["4-6"] + countsForSleepWithSoc1to3["7-8"] + countsForSleepWithSoc1to3[">8"]) * 100;
    let percentage1to3MoreThan8 = countsForSleepWithSoc1to3[">8"] / (countsForSleepWithSoc1to3["<4"] + countsForSleepWithSoc1to3["4-6"] + countsForSleepWithSoc1to3["7-8"] + countsForSleepWithSoc1to3[">8"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForSleepWithSoc1to3AsPercentage.push(percentage1to3LessThan4)
    countsForSleepWithSoc1to3AsPercentage.push(percentage1to34to6)
    countsForSleepWithSoc1to3AsPercentage.push(percentage1to37to8)
    countsForSleepWithSoc1to3AsPercentage.push(percentage1to3MoreThan8)

    
    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForSleepWithSoc1to3AsPercentage,
                        backgroundColor: colors}]
    // show data


    loaded.value = true;  
  }





  else if (amountOfSocMediaUse.value == "3 - 5 hours") {

      
    let countsForSleepWithSoc3to5 = {"<4": 0, "4-6": 0, "7-8": 0, ">8": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "3-5 hours") {
        if (amountOfSleepWithDup[i] === "Less than 4 hours") {
          countsForSleepWithSoc3to5["<4"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "4-6 hours") {
          countsForSleepWithSoc3to5["4-6"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "7-8 hours") {
          countsForSleepWithSoc3to5["7-8"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "More than 8 hours") {
          countsForSleepWithSoc3to5[">8"] += 1;
        }
      }
    }


    let countsForSleepWithSoc3to5AsPercentage = []

    // loads each count into a variable
    let percentage3to5LessThan4 = countsForSleepWithSoc3to5["<4"] / (countsForSleepWithSoc3to5["<4"] +countsForSleepWithSoc3to5["4-6"] + countsForSleepWithSoc3to5["7-8"] + countsForSleepWithSoc3to5[">8"]) * 100;
    let percentage3to54to6 = countsForSleepWithSoc3to5["4-6"] / (countsForSleepWithSoc3to5["<4"] + countsForSleepWithSoc3to5["4-6"] + countsForSleepWithSoc3to5["7-8"] + countsForSleepWithSoc3to5[">8"]) * 100;
    let percentage3to57to8 = countsForSleepWithSoc3to5["7-8"] / (countsForSleepWithSoc3to5["<4"] + countsForSleepWithSoc3to5["4-6"] + countsForSleepWithSoc3to5["7-8"] + countsForSleepWithSoc3to5[">8"]) * 100;
    let percentage3to5MoreThan8 = countsForSleepWithSoc3to5[">8"] / (countsForSleepWithSoc3to5["<4"] + countsForSleepWithSoc3to5["4-6"] + countsForSleepWithSoc3to5["7-8"] + countsForSleepWithSoc3to5[">8"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForSleepWithSoc3to5AsPercentage.push(percentage3to5LessThan4)
    countsForSleepWithSoc3to5AsPercentage.push(percentage3to54to6)
    countsForSleepWithSoc3to5AsPercentage.push(percentage3to57to8)
    countsForSleepWithSoc3to5AsPercentage.push(percentage3to5MoreThan8)

    
    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForSleepWithSoc3to5AsPercentage,
                        backgroundColor: colors}]
    // show data
    loaded.value = true;  
  } 



  else if (amountOfSocMediaUse.value == "more than 5 hours") {

      
    let countsForSleepWithSocMoreThan5 = {"<4": 0, "4-6": 0, "7-8": 0, ">8": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "More than 5 hours") {
        if (amountOfSleepWithDup[i] === "Less than 4 hours") {
          countsForSleepWithSocMoreThan5["<4"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "4-6 hours") {
          countsForSleepWithSocMoreThan5["4-6"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "7-8 hours") {
          countsForSleepWithSocMoreThan5["7-8"] += 1;
        }
        else if (amountOfSleepWithDup[i] === "More than 8 hours") {
          countsForSleepWithSocMoreThan5[">8"] += 1;
        }
      }
    }


    let countsForSleepWithSocMoreThan5AsPercentage = []

    // loads each count into a variable
    let percentageMoreThan5LessThan4 = countsForSleepWithSocMoreThan5["<4"] / (countsForSleepWithSocMoreThan5["<4"] +countsForSleepWithSocMoreThan5["4-6"] + countsForSleepWithSocMoreThan5["7-8"] + countsForSleepWithSocMoreThan5[">8"]) * 100;
    let percentageMoreThan54to6 = countsForSleepWithSocMoreThan5["4-6"] / (countsForSleepWithSocMoreThan5["<4"] + countsForSleepWithSocMoreThan5["4-6"] + countsForSleepWithSocMoreThan5["7-8"] + countsForSleepWithSocMoreThan5[">8"]) * 100;
    let percentageMoreThan57to8 = countsForSleepWithSocMoreThan5["7-8"] / (countsForSleepWithSocMoreThan5["<4"] + countsForSleepWithSocMoreThan5["4-6"] + countsForSleepWithSocMoreThan5["7-8"] + countsForSleepWithSocMoreThan5[">8"]) * 100;
    let percentageMoreThan5MoreThan8 = countsForSleepWithSocMoreThan5[">8"] / (countsForSleepWithSocMoreThan5["<4"] + countsForSleepWithSocMoreThan5["4-6"] + countsForSleepWithSocMoreThan5["7-8"] + countsForSleepWithSocMoreThan5[">8"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForSleepWithSocMoreThan5AsPercentage.push(percentageMoreThan5LessThan4)
    countsForSleepWithSocMoreThan5AsPercentage.push(percentageMoreThan54to6)
    countsForSleepWithSocMoreThan5AsPercentage.push(percentageMoreThan57to8)
    countsForSleepWithSocMoreThan5AsPercentage.push(percentageMoreThan5MoreThan8)


    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForSleepWithSocMoreThan5AsPercentage,
                        backgroundColor: colors}]
    // show data
    loaded.value = true;  
  }  
}
  const filteredChartData = computed(
    () => {
      return chartData.value    
    }
  );

</script>

<template>
  <div class="container">
    <!-- :chartLabel="amountOfSocMediaUse" -->
    <BarChart_Sleep_And_SocialMedia
      :labels="labelsforSleep"
      :datasets="filteredChartData"
      chartLabel="sleep"
      :amount="amountOfSocMediaUse"
    />
    <div class="row">
      <div class="col-1"></div>
      <div class="col">
        <button type="button" class="btn btn-primary" @click="loadFileWithUpdatedVar('less than 1 hour')">Less than 1 hour</button>
        <button type="button" class="btn btn-primary" @click="loadFileWithUpdatedVar('1 - 3 hours')">1 - 3 hours</button>
        <button type="button" class="btn btn-primary" @click="loadFileWithUpdatedVar('3 - 5 hours')">3 - 5 hours</button>
        <button type="button" class="btn btn-primary" @click="loadFileWithUpdatedVar('more than 5 hours')">More than 5 hours</button>
      </div>
      <div class="col"></div>
    </div>
  </div>
</template>