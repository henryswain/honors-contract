
<script setup>
  import Sleep from "./Sleep.vue"
  import Home from "./Home.vue"
  import Anxiety from "./Anxiety.vue"
  import Depression from "./Depression.vue"
  import {ref} from 'vue'
  const current = ref("Home")
</script>
<template>
  <div class="container">
    
    <!-- <div v-if="loaded"> we make sure we have the data before proceeding -->
  
      <!-- Title -->
      <nav class="navbar navbar-expand-lg bg-primary">
        <div class="container-fluid">

          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
            <div class="navbar-nav">
              
              <a :class="{'nav-link active': current == 'Home', 'nav-link': current != 'Home'}" aria-current="page" @click="current = 'Home'" style="cursor: pointer; color: white">Home</a>
              <a :class="{'nav-link active': current == 'Sleep', 'nav-link': current != 'Sleep'}" aria-current="page" @click="current = 'Sleep'" style="cursor: pointer; color: white">Sleep</a>
              <a :class="{'nav-link active': current == 'Anxiety', 'nav-link': current != 'Anxiety'}" aria-current="page" @click="current = 'Anxiety'" style="cursor: pointer; color: white">Anxiety</a>
              <a :class="{'nav-link active': current == 'Depression', 'nav-link': current != 'Depression'}" aria-current="page" @click="current = 'Depression'" style="cursor: pointer; color: white">Depression</a>
            </div>
          </div>
        </div>
      </nav>


      <div v-if="current == 'Sleep'">
        <Sleep/>
      </div>
      <div v-else-if="current == 'Home'">
        <Home/>
      </div>
      <div v-else-if="current == 'Anxiety'">
        <Anxiety/>
      </div>
      <div v-else>
        <Depression/>
      </div>
  </div>
</template>