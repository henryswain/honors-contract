<script setup>
  // to parse csv file
  import papaparse from 'papaparse';

  // bits of vue we are using
  import { ref, onMounted, computed } from 'vue';

  // import LineChart
  import LineChart from './components/LineChart.vue';

  // data imported
  const countryData = ref([]);

  // years in the data
  const years = ref([]);

  // data for line chart
  const chartData = ref([]);

  // tells us if the data has been loaded
  const loaded = ref(false);

  // for filtering based on text
  const filterText = ref('');

  // location of data
  const dataFile = "./src/assets/democracy-index-eiu.csv";

  // asynchronously loads data when page is mounted
  onMounted(() => fetch(dataFile)
    .then((res) => res.text())
    .then((text) => loadData(text))
    .then((parsedText) => showData(parsedText)));

  // asynchronous function to wrap around parse process
  async function loadData(dataText) { 
    console.log("ready to load data");
    let data = papaparse.parse(dataText, {delimter: ",", header: true});
    return data;
  }

  // get data ready to show
  function showData(parsedData) {
    console.log("data loaded");

    // get years
    // ... is called a spread operator
    // it extracts properties of what comes after and puts them in a new object (in this case an array)
    // map method on array maps each element in the array to another value
    // putting them in a Set object eliminates duplicates
    years.value = [...new Set(parsedData.data.map(item => item['Year']))];

    let countries = [...new Set(parsedData.data.map(item => item['Entity']))];

    // get data
    countryData.value = parsedData.data;

    countries.forEach(
      (country) => {
        // get numbers for this country
        let thisCountryData = parsedData.data.filter((row) => row['Entity'] == country);
        chartData.value.push({
          label: country,
          data: thisCountryData.map((row) => row['democracy_eiu']),
          borderColor: 'rgba(0, 0, 0, 0.1)'
        });
      }
    );

    // show data
    loaded.value = true;
  }

  const filteredChartData = computed(
    () => {
      // finally, filter by text entered by user
      if (filterText.value.length > 0) {
        return chartData.value.filter((country) => country.label.includes(filterText.value));
      } else {
        return chartData.value;
      }      
    }
  );

</script>

<template>
  <div class="container">
    
    <div v-if="loaded"> <!-- we make sure we have the data before proceeding -->

      <!-- Title -->
      <div class="row">
        <h1>Democracy Index by Country</h1>
        <hr/>
      </div>

      <!-- Filtering -->
      <div class="row">
        <div class="col">

          <!-- text filter -->
          <div class="mb-3">
            <label for="countryFilter" class="form-label">Filter countries/regions</label>
            <input type="text" class="form-control" id="countryFilter" v-model="filterText">
          </div>
          <hr/>
        </div>
      </div>

      <!-- Chart -->
      <LineChart 
        :labels="years"
        :datasets="filteredChartData"
        chartLabel="Democracy Index"
      />
    </div>
    <div v-else> <!-- Shows before data is loaded -->
      <p>Loading data...</p>
    </div>
  </div>
</template>