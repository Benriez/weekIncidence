<template>
  <q-page class="flex column topSearch" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        @keyup.enter="getWeatherbySearch"
        bottom-slots
        v-model="search"
        placeholder="Search"
        dark
        borderless=""
        >
        <template v-slot:before>
          <q-icon
            @click="getLocation"
            name="my_location" />
        </template>


        <template v-slot:append>
          <q-btn
            @click="getWeatherbySearch"
            round
            dense
            flat
            icon="search" />
        </template>
      </q-input>
    </div>

    

    <template v-if="weatherData">
      <div class="col text-white text-center textData">
        <div class="text-h4 text-weight-light">
          {{weatherData.name}}
        </div>
        <div class="text-h6 text-weight-light">
          {{coronaData.data[gemeindezahl].county}}
        </div>
        <div class="text-h6 text-weight-light">
          7 Tage Inzidenz
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(coronaData.data[gemeindezahl].weekIncidence)}}</span> 
        </div>
      </div>

      <!-- <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div> -->

    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin intro">
          What's my <br> Incidence
        </div>
          <q-btn
            @click="getLocation"
            class="col geLocationBtn"
            flat>
            <q-icon left size="3em" name="my_location" />
            <div>Find my location</div>
          </q-btn>
      </div>
    </template>

    <div class="wrapper">
      <div class="box">
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
        <div></div>
      </div>
    </div>

  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data(){
    return{
      search: '',
      weatherData: null,
      coronaData: null,
      weekIncidence: '',
      gemeindezahl: null,
      lat: null,
      lon: null,
      apiURL:'https://api.openweathermap.org/data/2.5/weather',
      apiCoronaURL: 'https://api.corona-zahlen.org/districts/',
      apiKey: '56bc2a1374eb7daa0e9aa172391c3899'
    }
  },
  computed: {
    bgClass(){
      if(this.weatherData){
        if(this.weatherData.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }
        else {
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation(){
      this.$q.loading.show()
      navigator.geolocation.getCurrentPosition(position => {
        this.lat= position.coords.latitude
        this.lon= position.coords.longitude
        this.getWeatherByCoords()
      })
    },
    getWeatherByCoords(){
      this.$q.loading.show()
      this.$axios(`${this.apiURL}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        this.get_ags()
        this.$q.loading.hide()
        // https://www.dcat-ap.de/def/politicalGeocoding/municipalityKey/20210131.html
      })
    },
    getWeatherbySearch(){
      this.$q.loading.show()
      this.$axios(`${this.apiURL}?q=${this.search}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        // this.getAGS()
        this.get_ags()
      })
      this.$q.loading.hide()
    },
    get_ags(){
      console.log('what up')
      var ort = this.weatherData.name
      var ags = '';
      this.$axios.get('ags.txt').then(response=>{
        for (let index = 0; index < 5; index++) {
          ags = ags + response.data.charAt(response.data.indexOf(ort)-27 + index)    
        }
        this.gemeindezahl = ags
        console.log('axios get gemeindezahl::', this.gemeindezahl)

        this.getCoronaStats()
      })

    }, 
    getAGS(){
      // var ort = this.weatherData.name
      // var ags = '';
      
      // jQuery.get('ags.txt',function(data){
      //   for (let index = 0; index < 5; index++) {
      //     ags = ags + data.charAt(data.indexOf(ort)-27 + index)    
      //   }
      //   this.gemeindezahl = ags
      //   console.log('jquery get gemeindezahl::', this.gemeindezahl)
        
      //   jQuery.get(`https://api.corona-zahlen.org/districts/`+this.gemeindezahl).then(response=>{
      //     this.coronaData = response.data
      //     console.log('c-data', this.coronaData)
      //   });

      // });
    },
    getCoronaStats(){
      console.log('get c-data', this.gemeindezahl)

      this.$axios(`https://api.corona-zahlen.org/districts/${this.gemeindezahl}`).then(response=>{
        this.coronaData = response.data
        console.log('c-data', this.coronaData)
      });
    
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to top, #712c57, #000000)
    &.bg-night
      background: linear-gradient(to bottom, #712c57, #000000)
    &.bg-day
      background: linear-gradient(to top, #712c57, #000000)
  .degree
    top: -44px

  // .skyline
  //   flex: 0 0 150px
  //   background: url(../assets/skyline.png)
  //   background-size: contain
  //   background-position: center bottom

  body
    margin: 0
    padding: 0

  .geLocationBtn
    z-index: 10
  
  .topSearch
    z-index: 11
  
  .intro
    margin-top: -5rem

  .wrapper
    position: absolute
    width: 100%
    height: 100%
    overflow: hidden
    z-index: -1
  
  .textData
    margin-top: -5rem
  
  .box div
    position: absolute
    width: 60px
    height: 60px
    background-color: transparent
    border: 6px solid #efefef

  .box div:nth-child(1)
    top: 12%
    left: 42%
    animation: animate 10s linear infinite
  
  .box div:nth-child(2)
    top: 85%
    left: 50%
    animation: animate 7s linear infinite
  
  .box div:nth-child(3)
    top: 17%
    left: 6%
    animation: animate 9s linear infinite
  
  .box div:nth-child(4)
    top: 20%
    left: 60%
    animation: animate 10s linear infinite
  
  .box div:nth-child(5)
    top: 67%
    left: 10%
    animation: animate 6s linear infinite
  
  .box div:nth-child(6)
    top: 80%
    left: 70%
    animation: animate 12s linear infinite
  
  .box div:nth-child(7)
    top: 60%
    left: 80%
    animation: animate 15s linear infinite
  
  .box div:nth-child(8)
    top: 32%
    left: 25%
    animation: animate 16s linear infinite

  .box div:nth-child(9)
    top: 90%
    left: 25%
    animation: animate 9s linear infinite
  
  .box div:nth-child(10)
    top: 20%
    left: 80%
    animation: animate 5s linear infinite

  @keyframes animate
    0%
      transform: scale(0) translateY(0) rotate(0)
      opacity: 1
    100%
      transform: scale(1.3) translateY(-90px) rotate(360deg)
      opacity: 0

</style>
