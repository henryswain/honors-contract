<script setup>
// to parse csv file
    import papaparse from 'papaparse';


    const labelsforDepression = [["wasn't depressed", "at all in", "the last 30 days"], ["was depressed", "several days", "in the last 30 days"], ["was depressed", "half of the days", "in the last 30 days"], ["was depressed", "nearly every day", "in the last 30 days"]]
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

    const dataFile = "/honors-contract/bangladesh_social_media_mental_health_dataset.csv";

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
    let amountOfDepressionWithDup = []
    for (let i of parsedData.data) {
        freqTimeOnSocMediaWithDup.push(i['7. How much time do you spend daily in social media?'])
        amountOfDepressionWithDup.push(i['2. In the last 30 days, feeling down, depressed or hopeless.'])
    }

    let numberOfRows = []
    for (let i = 0; i < freqTimeOnSocMediaWithDup.length; i++) {
        numberOfRows.push(i);
    }

    if (amountOfSocMediaUse.value == 'less than 1 hour') {
        // creates an object with the counts for each depression categorory with social media use less than 1 hour

        let countsForDepressionWithSocLessThan1 = {"1": 0, "2": 0, "3": 0, "4": 0};
        for (let i of numberOfRows) {
            if (freqTimeOnSocMediaWithDup[i] === "Less than 1 hour") {
                if (amountOfDepressionWithDup[i] === "Not at all") {
                countsForDepressionWithSocLessThan1["1"] += 1;
                }
                else if (amountOfDepressionWithDup[i] === "Several days") {
                countsForDepressionWithSocLessThan1["2"] += 1;
                }
                else if (amountOfDepressionWithDup[i] === "Half days") {
                countsForDepressionWithSocLessThan1["3"] += 1;
                }
                else if (amountOfDepressionWithDup[i] === "Nearly everyday") {
                countsForDepressionWithSocLessThan1["4"] += 1;
                }
            }
        }


        let countsForDepressionWithSocLessThan1AsPercentage = []

        // loads each count into a variable
        let percentageLessThan11 = countsForDepressionWithSocLessThan1["1"] / (countsForDepressionWithSocLessThan1["1"] + countsForDepressionWithSocLessThan1["2"] + countsForDepressionWithSocLessThan1["3"] + countsForDepressionWithSocLessThan1["4"]) * 100;
        let percentageLessThan12 = countsForDepressionWithSocLessThan1["2"] / (countsForDepressionWithSocLessThan1["1"] + countsForDepressionWithSocLessThan1["2"] + countsForDepressionWithSocLessThan1["3"] + countsForDepressionWithSocLessThan1["4"]) * 100;
        let percentageLessThan13 = countsForDepressionWithSocLessThan1["3"] / (countsForDepressionWithSocLessThan1["1"] + countsForDepressionWithSocLessThan1["2"] + countsForDepressionWithSocLessThan1["3"] + countsForDepressionWithSocLessThan1["4"]) * 100;
        let percentageLessThan14 = countsForDepressionWithSocLessThan1["4"] / (countsForDepressionWithSocLessThan1["1"] + countsForDepressionWithSocLessThan1["2"] + countsForDepressionWithSocLessThan1["3"] + countsForDepressionWithSocLessThan1["4"]) * 100;

        // adds those variables into countsForSleepForLessThan1AsPercentage
        countsForDepressionWithSocLessThan1AsPercentage.push(percentageLessThan11)
        countsForDepressionWithSocLessThan1AsPercentage.push(percentageLessThan12)
        countsForDepressionWithSocLessThan1AsPercentage.push(percentageLessThan13)
        countsForDepressionWithSocLessThan1AsPercentage.push(percentageLessThan14)

        
        const colors = ["red", "orange", "green", "blue"]
        chartData.value = [{label: "Percentage of participants",
        data: countsForDepressionWithSocLessThan1AsPercentage,
                            backgroundColor: colors}]
        // show data

        console.log("chartData.value " + JSON.stringify(chartData.value))
        loaded.value = true;
    }

    else if (amountOfSocMediaUse.value == "1 - 3 hours") {
        // creates an object with the counts for each depression categorory with social media use between 1 and 3 hours


        let countsForDepressionWithSoc1to3 = {"1": 0, "2": 0, "3": 0, "4": 0};
        for (let i of numberOfRows) {
        if (freqTimeOnSocMediaWithDup[i] === "1-3 hours") {
            if (amountOfDepressionWithDup[i] === "Not at all") {
            countsForDepressionWithSoc1to3["1"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Several days") {
            countsForDepressionWithSoc1to3["2"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Half days") {
            countsForDepressionWithSoc1to3["3"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Nearly everyday") {
            countsForDepressionWithSoc1to3["4"] += 1;
            }
        }
        }


        let countsForDepressionWithSoc1to3AsPercentage = []

        // loads each count into a variable
        let percentage1to31 = countsForDepressionWithSoc1to3["1"] / (countsForDepressionWithSoc1to3["1"] + countsForDepressionWithSoc1to3["2"] + countsForDepressionWithSoc1to3["3"] + countsForDepressionWithSoc1to3["4"]) * 100;
        let percentage1to32 = countsForDepressionWithSoc1to3["2"] / (countsForDepressionWithSoc1to3["1"] + countsForDepressionWithSoc1to3["2"] + countsForDepressionWithSoc1to3["3"] + countsForDepressionWithSoc1to3["4"]) * 100;
        let percentage1to33 = countsForDepressionWithSoc1to3["3"] / (countsForDepressionWithSoc1to3["1"] + countsForDepressionWithSoc1to3["2"] + countsForDepressionWithSoc1to3["3"] + countsForDepressionWithSoc1to3["4"]) * 100;
        let percentage1to34 = countsForDepressionWithSoc1to3["4"] / (countsForDepressionWithSoc1to3["1"] + countsForDepressionWithSoc1to3["2"] + countsForDepressionWithSoc1to3["3"] + countsForDepressionWithSoc1to3["4"]) * 100;

        // adds those variables into countsForSleepForLessThan1AsPercentage
        countsForDepressionWithSoc1to3AsPercentage.push(percentage1to31)
        countsForDepressionWithSoc1to3AsPercentage.push(percentage1to32)
        countsForDepressionWithSoc1to3AsPercentage.push(percentage1to33)
        countsForDepressionWithSoc1to3AsPercentage.push(percentage1to34)

        
        const colors = ["red", "orange", "green", "blue"]
        chartData.value = [{label: "Percentage of participants",
        data: countsForDepressionWithSoc1to3AsPercentage,
                            backgroundColor: colors}]
        // show data


        loaded.value = true;  
    }





    else if (amountOfSocMediaUse.value == "3 - 5 hours") {

        // creates an object with the counts for each depression categorory with social media use between 2 and 5 hours

        let countsForDepressionWithSoc3to5 = {"1": 0, "2": 0, "3": 0, "4": 0};
        for (let i of numberOfRows) {
        if (freqTimeOnSocMediaWithDup[i] === "3-5 hours") {
            if (amountOfDepressionWithDup[i] === "Not at all") {
            countsForDepressionWithSoc3to5["1"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Several days") {
            countsForDepressionWithSoc3to5["2"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Half days") {
            countsForDepressionWithSoc3to5["3"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Nearly every day") {
            countsForDepressionWithSoc3to5["4"] += 1;
            }
        }
        }


        let countsForDepressionWithSoc3to5AsPercentage = []

        // loads each count into a variable
        let percentage3to51 = countsForDepressionWithSoc3to5["1"] / (countsForDepressionWithSoc3to5["1"] + countsForDepressionWithSoc3to5["2"] + countsForDepressionWithSoc3to5["3"] + countsForDepressionWithSoc3to5["4"]) * 100;
        let percentage3to52 = countsForDepressionWithSoc3to5["2"] / (countsForDepressionWithSoc3to5["1"] + countsForDepressionWithSoc3to5["2"] + countsForDepressionWithSoc3to5["3"] + countsForDepressionWithSoc3to5["4"]) * 100;
        let percentage3to53 = countsForDepressionWithSoc3to5["3"] / (countsForDepressionWithSoc3to5["1"] + countsForDepressionWithSoc3to5["2"] + countsForDepressionWithSoc3to5["3"] + countsForDepressionWithSoc3to5["4"]) * 100;
        let percentage3to54 = countsForDepressionWithSoc3to5["4"] / (countsForDepressionWithSoc3to5["1"] + countsForDepressionWithSoc3to5["2"] + countsForDepressionWithSoc3to5["3"] + countsForDepressionWithSoc3to5["4"]) * 100;

        // adds those variables into countsForSleepForLessThan1AsPercentage
        countsForDepressionWithSoc3to5AsPercentage.push(percentage3to51)
        countsForDepressionWithSoc3to5AsPercentage.push(percentage3to52)
        countsForDepressionWithSoc3to5AsPercentage.push(percentage3to53)
        countsForDepressionWithSoc3to5AsPercentage.push(percentage3to54)

        
        const colors = ["red", "orange", "green", "blue"]
        chartData.value = [{label: "Percentage of participants",
        data: countsForDepressionWithSoc3to5AsPercentage,
                            backgroundColor: colors}]
        // show data
        loaded.value = true;  
    } 



    else if (amountOfSocMediaUse.value == "more than 5 hours") {

        // creates an object with the counts for each depression categorory with social media use more than 5 hours

        let countsForDepressionWithSocMoreThan5 = {"1": 0, "2": 0, "3": 0, "4": 0};
        for (let i of numberOfRows) {
        if (freqTimeOnSocMediaWithDup[i] === "More than 5 hours") {
            if (amountOfDepressionWithDup[i] === "Not at all") {
            countsForDepressionWithSocMoreThan5["1"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Several days") {
            countsForDepressionWithSocMoreThan5["2"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Half days") {
            countsForDepressionWithSocMoreThan5["3"] += 1;
            }
            else if (amountOfDepressionWithDup[i] === "Nearly every day") {
            countsForDepressionWithSocMoreThan5["4"] += 1;
            }
        }
        }


        let countsForDepressionWithSocMoreThan5AsPercentage = []

        // loads each count into a variable
        let percentageMoreThan51 = countsForDepressionWithSocMoreThan5["1"] / (countsForDepressionWithSocMoreThan5["1"] + countsForDepressionWithSocMoreThan5["2"] + countsForDepressionWithSocMoreThan5["3"] + countsForDepressionWithSocMoreThan5["4"]) * 100;
        let percentageMoreThan52 = countsForDepressionWithSocMoreThan5["2"] / (countsForDepressionWithSocMoreThan5["1"] + countsForDepressionWithSocMoreThan5["2"] + countsForDepressionWithSocMoreThan5["3"] + countsForDepressionWithSocMoreThan5["4"]) * 100;
        let percentageMoreThan53 = countsForDepressionWithSocMoreThan5["3"] / (countsForDepressionWithSocMoreThan5["1"] + countsForDepressionWithSocMoreThan5["2"] + countsForDepressionWithSocMoreThan5["3"] + countsForDepressionWithSocMoreThan5["4"]) * 100;
        let percentageMoreThan54 = countsForDepressionWithSocMoreThan5["4"] / (countsForDepressionWithSocMoreThan5["1"] + countsForDepressionWithSocMoreThan5["2"] + countsForDepressionWithSocMoreThan5["3"] + countsForDepressionWithSocMoreThan5["4"]) * 100;

        // adds those variables into countsForSleepForLessThan1AsPercentage
        countsForDepressionWithSocMoreThan5AsPercentage.push(percentageMoreThan51)
        countsForDepressionWithSocMoreThan5AsPercentage.push(percentageMoreThan52)
        countsForDepressionWithSocMoreThan5AsPercentage.push(percentageMoreThan53)
        countsForDepressionWithSocMoreThan5AsPercentage.push(percentageMoreThan54)


        const colors = ["red", "orange", "green", "blue"]
        chartData.value = [{label: "Percentage of participants",
        data: countsForDepressionWithSocMoreThan5AsPercentage,
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

// update button classes onclick
const updateButtonClasses = (category) => {

  if (category == "<1") {
    // update <1 cateogoy
    document.getElementById("<1").classList.remove("btn-primary")
    document.getElementById("<1").classList.remove("btn-outline-primary")
    document.getElementById("<1").classList.add("btn-primary")

    // update 1-3 cateogoy
    document.getElementById("1-3").classList.remove("btn-primary")
    document.getElementById("1-3").classList.remove("btn-outline-primary")
    document.getElementById("1-3").classList.add("btn-outline-primary")

    // update 3-5 cateogoy
    document.getElementById("3-5").classList.remove("btn-primary")
    document.getElementById("3-5").classList.remove("btn-outline-primary")
    document.getElementById("3-5").classList.add("btn-outline-primary")

    // update >5 cateogoy
    document.getElementById(">5").classList.remove("btn-primary")
    document.getElementById(">5").classList.remove("btn-outline-primary")
    document.getElementById(">5").classList.add("btn-outline-primary")
  }


  else if (category == "1-3") {
    // update 1-3 cateogoy
    document.getElementById("1-3").classList.remove("btn-primary")
    document.getElementById("1-3").classList.remove("btn-outline-primary")
    document.getElementById("1-3").classList.add("btn-primary")

    // update <1 cateogoy
    document.getElementById("<1").classList.remove("btn-primary")
    document.getElementById("<1").classList.remove("btn-outline-primary")
    document.getElementById("<1").classList.add("btn-outline-primary")

    // update 3-5 cateogoy
    document.getElementById("3-5").classList.remove("btn-primary")
    document.getElementById("3-5").classList.remove("btn-outline-primary")
    document.getElementById("3-5").classList.add("btn-outline-primary")

    // update >5 cateogoy
    document.getElementById(">5").classList.remove("btn-primary")
    document.getElementById(">5").classList.remove("btn-outline-primary")
    document.getElementById(">5").classList.add("btn-outline-primary")
  }

  if (category == "3-5") {
    // update 3-5 cateogoy
    document.getElementById("3-5").classList.remove("btn-primary")
    document.getElementById("3-5").classList.remove("btn-outline-primary")
    document.getElementById("3-5").classList.add("btn-primary")

    // update 1-3 cateogoy
    document.getElementById("1-3").classList.remove("btn-primary")
    document.getElementById("1-3").classList.remove("btn-outline-primary")
    document.getElementById("1-3").classList.add("btn-outline-primary")

    // update <1 cateogoy
    document.getElementById("<1").classList.remove("btn-primary")
    document.getElementById("<1").classList.remove("btn-outline-primary")
    document.getElementById("<1").classList.add("btn-outline-primary")

    // update >5 cateogoy
    document.getElementById(">5").classList.remove("btn-primary")
    document.getElementById(">5").classList.remove("btn-outline-primary")
    document.getElementById(">5").classList.add("btn-outline-primary")
  }

  if (category == ">5") {
    // update >5 cateogoy
    document.getElementById(">5").classList.remove("btn-primary")
    document.getElementById(">5").classList.remove("btn-outline-primary")
    document.getElementById(">5").classList.add("btn-primary")

    // update 1-3 cateogoy
    document.getElementById("1-3").classList.remove("btn-primary")
    document.getElementById("1-3").classList.remove("btn-outline-primary")
    document.getElementById("1-3").classList.add("btn-outline-primary")

    // update 3-5 cateogoy
    document.getElementById("3-5").classList.remove("btn-primary")
    document.getElementById("3-5").classList.remove("btn-outline-primary")
    document.getElementById("3-5").classList.add("btn-outline-primary")

    // update <1 cateogoy
    document.getElementById("<1").classList.remove("btn-primary")
    document.getElementById("<1").classList.remove("btn-outline-primary")
    document.getElementById("<1").classList.add("btn-outline-primary")
  }
}
</script>

<template>
    <!-- button group for handling social media category chagnes-->
    <div class="row justify-content-center mt-2">
      <div class="col-3">
        <button type="button" class="btn btn-primary" @click="loadFileWithUpdatedVar('less than 1 hour'); updateButtonClasses('<1')" id="<1">Less than 1 hour</button>
      </div>
      <div class="col-3">
        <button type="button" class="btn btn-outline-primary" @click="loadFileWithUpdatedVar('1 - 3 hours'); updateButtonClasses('1-3')" id="1-3">1 - 3 hours</button>
      </div>
      <div class="col-3">
        <button type="button" class="btn btn-outline-primary" @click="loadFileWithUpdatedVar('3 - 5 hours'); updateButtonClasses('3-5')" id="3-5">3 - 5 hours</button>
      </div>
      <div class="col-3">
        <button type="button" class="btn btn-outline-primary" @click="loadFileWithUpdatedVar('more than 5 hours'); updateButtonClasses('>5')" id=">5">More than 5 hours</button>
      </div>
    </div>
    <BarChart_Sleep_And_SocialMedia
      :labels="labelsforDepression"
      :datasets="filteredChartData"
      chartLabel="depression"
      :amount="amountOfSocMediaUse"
    />
</template>