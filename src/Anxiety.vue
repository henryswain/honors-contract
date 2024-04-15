<script setup>
// to parse csv file
import papaparse from 'papaparse';


const labelsforAnxiety = ["wasn't anxious at all in the last 30 days", "was anxious several days in the last 30 days", "was anxious half of the days in the last 30 days", "was nearly every day in the last 30 days"]
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
  let amountOfAnxietyWithDup = []
  for (let i of parsedData.data) {
    freqTimeOnSocMediaWithDup.push(i[['7. How much time do you spend daily in social media?']])
    amountOfAnxietyWithDup.push(i['1. In the last 30 days, I am feeling nervous, anxious, or on edge'])
  }

  let numberOfRows = []
  for (let i = 0; i < freqTimeOnSocMediaWithDup.length; i++) {
    numberOfRows.push(i);
  }

// creates an object with the counts of each amount of time on social media with sleep less than 4 hours
  if (amountOfSocMediaUse.value == 'less than 1 hour') {

    let countsForAnxietyWithSocLessThan1 = {"1": 0, "2": 0, "3": 0, "4": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "Less than 1 hour") {
        if (amountOfAnxietyWithDup[i] === "Not at all") {
          countsForAnxietyWithSocLessThan1["1"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Several days") {
          countsForAnxietyWithSocLessThan1["2"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Half days") {
          countsForAnxietyWithSocLessThan1["3"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Nearly everyday") {
          countsForAnxietyWithSocLessThan1["4"] += 1;
        }
      }
    }


    let countsForAnxietyWithSocLessThan1AsPercentage = []

    // loads each count into a variable
    let percentageLessThan11 = countsForAnxietyWithSocLessThan1["1"] / (countsForAnxietyWithSocLessThan1["1"] + countsForAnxietyWithSocLessThan1["2"] + countsForAnxietyWithSocLessThan1["3"] + countsForAnxietyWithSocLessThan1["4"]) * 100;
    let percentageLessThan12 = countsForAnxietyWithSocLessThan1["2"] / (countsForAnxietyWithSocLessThan1["1"] + countsForAnxietyWithSocLessThan1["2"] + countsForAnxietyWithSocLessThan1["3"] + countsForAnxietyWithSocLessThan1["4"]) * 100;
    let percentageLessThan13 = countsForAnxietyWithSocLessThan1["3"] / (countsForAnxietyWithSocLessThan1["1"] + countsForAnxietyWithSocLessThan1["2"] + countsForAnxietyWithSocLessThan1["3"] + countsForAnxietyWithSocLessThan1["4"]) * 100;
    let percentageLessThan14 = countsForAnxietyWithSocLessThan1["4"] / (countsForAnxietyWithSocLessThan1["1"] + countsForAnxietyWithSocLessThan1["2"] + countsForAnxietyWithSocLessThan1["3"] + countsForAnxietyWithSocLessThan1["4"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForAnxietyWithSocLessThan1AsPercentage.push(percentageLessThan11)
    countsForAnxietyWithSocLessThan1AsPercentage.push(percentageLessThan12)
    countsForAnxietyWithSocLessThan1AsPercentage.push(percentageLessThan13)
    countsForAnxietyWithSocLessThan1AsPercentage.push(percentageLessThan14)

    
    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForAnxietyWithSocLessThan1AsPercentage,
                        backgroundColor: colors}]
    // show data

    console.log("chartData.value " + JSON.stringify(chartData.value))
    loaded.value = true;
  }

  else if (amountOfSocMediaUse.value == "1 - 3 hours") {


    let countsForAnxietyWithSoc1to3 = {"1": 0, "2": 0, "3": 0, "4": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "1-3 hours") {
        if (amountOfAnxietyWithDup[i] === "Not at all") {
          countsForAnxietyWithSoc1to3["1"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Several days") {
          countsForAnxietyWithSoc1to3["2"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Half days") {
          countsForAnxietyWithSoc1to3["3"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Nearly everyday") {
          countsForAnxietyWithSoc1to3["4"] += 1;
        }
      }
    }


    let countsForAnxietyWithSoc1to3AsPercentage = []

    // loads each count into a variable
    let percentage1to31 = countsForAnxietyWithSoc1to3["1"] / (countsForAnxietyWithSoc1to3["1"] + countsForAnxietyWithSoc1to3["2"] + countsForAnxietyWithSoc1to3["3"] + countsForAnxietyWithSoc1to3["4"]) * 100;
    let percentage1to32 = countsForAnxietyWithSoc1to3["2"] / (countsForAnxietyWithSoc1to3["1"] + countsForAnxietyWithSoc1to3["2"] + countsForAnxietyWithSoc1to3["3"] + countsForAnxietyWithSoc1to3["4"]) * 100;
    let percentage1to33 = countsForAnxietyWithSoc1to3["3"] / (countsForAnxietyWithSoc1to3["1"] + countsForAnxietyWithSoc1to3["2"] + countsForAnxietyWithSoc1to3["3"] + countsForAnxietyWithSoc1to3["4"]) * 100;
    let percentage1to34 = countsForAnxietyWithSoc1to3["4"] / (countsForAnxietyWithSoc1to3["1"] + countsForAnxietyWithSoc1to3["2"] + countsForAnxietyWithSoc1to3["3"] + countsForAnxietyWithSoc1to3["4"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForAnxietyWithSoc1to3AsPercentage.push(percentage1to31)
    countsForAnxietyWithSoc1to3AsPercentage.push(percentage1to32)
    countsForAnxietyWithSoc1to3AsPercentage.push(percentage1to33)
    countsForAnxietyWithSoc1to3AsPercentage.push(percentage1to34)

    
    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForAnxietyWithSoc1to3AsPercentage,
                        backgroundColor: colors}]
    // show data


    loaded.value = true;  
  }





  else if (amountOfSocMediaUse.value == "3 - 5 hours") {

      
    let countsForAnxietyWithSoc3to5 = {"1": 0, "2": 0, "3": 0, "4": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "3-5 hours") {
        if (amountOfAnxietyWithDup[i] === "Not at all") {
          countsForAnxietyWithSoc3to5["1"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Several days") {
          countsForAnxietyWithSoc3to5["2"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Half days") {
          countsForAnxietyWithSoc3to5["3"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Nearly every day") {
          countsForAnxietyWithSoc3to5["4"] += 1;
        }
      }
    }


    let countsForAnxietyWithSoc3to5AsPercentage = []

    // loads each count into a variable
    let percentage3to51 = countsForAnxietyWithSoc3to5["1"] / (countsForAnxietyWithSoc3to5["1"] + countsForAnxietyWithSoc3to5["2"] + countsForAnxietyWithSoc3to5["3"] + countsForAnxietyWithSoc3to5["4"]) * 100;
    let percentage3to52 = countsForAnxietyWithSoc3to5["2"] / (countsForAnxietyWithSoc3to5["1"] + countsForAnxietyWithSoc3to5["2"] + countsForAnxietyWithSoc3to5["3"] + countsForAnxietyWithSoc3to5["4"]) * 100;
    let percentage3to53 = countsForAnxietyWithSoc3to5["3"] / (countsForAnxietyWithSoc3to5["1"] + countsForAnxietyWithSoc3to5["2"] + countsForAnxietyWithSoc3to5["3"] + countsForAnxietyWithSoc3to5["4"]) * 100;
    let percentage3to54 = countsForAnxietyWithSoc3to5["4"] / (countsForAnxietyWithSoc3to5["1"] + countsForAnxietyWithSoc3to5["2"] + countsForAnxietyWithSoc3to5["3"] + countsForAnxietyWithSoc3to5["4"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForAnxietyWithSoc3to5AsPercentage.push(percentage3to51)
    countsForAnxietyWithSoc3to5AsPercentage.push(percentage3to52)
    countsForAnxietyWithSoc3to5AsPercentage.push(percentage3to53)
    countsForAnxietyWithSoc3to5AsPercentage.push(percentage3to54)

    
    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForAnxietyWithSoc3to5AsPercentage,
                        backgroundColor: colors}]
    // show data
    loaded.value = true;  
  } 



  else if (amountOfSocMediaUse.value == "more than 5 hours") {

      
    let countsForAnxietyWithSocMoreThan5 = {"1": 0, "2": 0, "3": 0, "4": 0};
    for (let i of numberOfRows) {
      if (freqTimeOnSocMediaWithDup[i] === "More than 5 hours") {
        if (amountOfAnxietyWithDup[i] === "Not at all") {
          countsForAnxietyWithSocMoreThan5["1"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Several days") {
          countsForAnxietyWithSocMoreThan5["2"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Half days") {
          countsForAnxietyWithSocMoreThan5["3"] += 1;
        }
        else if (amountOfAnxietyWithDup[i] === "Nearly every day") {
          countsForAnxietyWithSocMoreThan5["4"] += 1;
        }
      }
    }


    let countsForAnxietyWithSocMoreThan5AsPercentage = []

    // loads each count into a variable
    let percentageMoreThan51 = countsForAnxietyWithSocMoreThan5["1"] / (countsForAnxietyWithSocMoreThan5["1"] + countsForAnxietyWithSocMoreThan5["2"] + countsForAnxietyWithSocMoreThan5["3"] + countsForAnxietyWithSocMoreThan5["4"]) * 100;
    let percentageMoreThan52 = countsForAnxietyWithSocMoreThan5["2"] / (countsForAnxietyWithSocMoreThan5["1"] + countsForAnxietyWithSocMoreThan5["2"] + countsForAnxietyWithSocMoreThan5["3"] + countsForAnxietyWithSocMoreThan5["4"]) * 100;
    let percentageMoreThan53 = countsForAnxietyWithSocMoreThan5["3"] / (countsForAnxietyWithSocMoreThan5["1"] + countsForAnxietyWithSocMoreThan5["2"] + countsForAnxietyWithSocMoreThan5["3"] + countsForAnxietyWithSocMoreThan5["4"]) * 100;
    let percentageMoreThan54 = countsForAnxietyWithSocMoreThan5["4"] / (countsForAnxietyWithSocMoreThan5["1"] + countsForAnxietyWithSocMoreThan5["2"] + countsForAnxietyWithSocMoreThan5["3"] + countsForAnxietyWithSocMoreThan5["4"]) * 100;

    // adds those variables into countsForSleepForLessThan1AsPercentage
    countsForAnxietyWithSocMoreThan5AsPercentage.push(percentageMoreThan51)
    countsForAnxietyWithSocMoreThan5AsPercentage.push(percentageMoreThan52)
    countsForAnxietyWithSocMoreThan5AsPercentage.push(percentageMoreThan53)
    countsForAnxietyWithSocMoreThan5AsPercentage.push(percentageMoreThan54)


    const colors = ["red", "orange", "green", "blue"]
    chartData.value = [{label: "Percentage of participants",
    data: countsForAnxietyWithSocMoreThan5AsPercentage,
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

    <BarChart_Sleep_And_SocialMedia
      :labels="labelsforAnxiety"
      :datasets="filteredChartData"
      :chartLabel="amountOfSocMediaUse"
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