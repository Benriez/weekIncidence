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

    <template v-if="coronaData">
      <div class="col result text-white text-center textData">
        <div class="text-h4 weatherData-name">
          {{weatherData.name}}
        </div>
        <div class="text-h6 text-weight-light">
          {{coronaData.data[gemeindezahl].county}}
        </div>
        <div class="text-h6 text-weight-light">
          7 Tage Inzidenz
        </div>
        <template v-if="coronaData.data[gemeindezahl].weekIncidence < 50">
          <div class="text-h1 text-weight-light q-my-lg relative-position" style="color: green">
            <span>{{ Math.round(coronaData.data[gemeindezahl].weekIncidence)}}</span> 
          </div>
        </template>
        <template v-else-if="coronaData.data[gemeindezahl].weekIncidence > 50 && coronaData.data[gemeindezahl].weekIncidence < 100">
          <div class="text-h1 text-weight-light q-my-lg relative-position" style="color: orange">
            <span>{{ Math.round(coronaData.data[gemeindezahl].weekIncidence)}}</span> 
          </div>
        </template>
        <template v-else>
          <div class="text-h1 text-weight-light q-my-lg relative-position" style="color: red">
            <span>{{ Math.round(coronaData.data[gemeindezahl].weekIncidence)}}</span> 
          </div>
        </template>
      </div>
      <div class="meta-data text-center">
        <span>Source: {{ coronaData.meta.source }}</span>
        <span>Last Update: {{ coronaData.meta.lastUpdate.split(".")[0] }}</span>
        <span class="meta-data-div">Data provided by: {{ coronaData.meta.contact.split("(")[0]  }}</span>
        <span class="donation">
          <img class="buymecoffee-img" src="buymeacoffee.svg" height="23px" />
          <a class="buymecoffee"  href="https://www.paypal.com/donate?hosted_button_id=VUDEMUZAVN3Q2"> Buy me a coffee</a>
        </span> 
        
      </div>
      <!-- <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div> -->

    </template>

    <template v-else>
      <a href='https://www.symptoma.ro/'>Căutare de informații medicale</a>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin intro">
          What's my <br> Incidence
        </div>
          <q-btn
            @click="getLocation"
            class="col getLocationBtn"
            flat>
            <q-icon left size="3em" name="my_location" />
            <div>Find my location</div>
          </q-btn>
      </div> 
    </template>

    <!-- data from && data provided by -->
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
    <!-- <a href="https://www.checkdomain.de/unternehmen/garantie/ssl/popup/" onclick="window.open(this.href + '?host=' + window.location.host,'','height=600,width=560,scrollbars=yes'); return false;"><img src="https://www.checkdomain.de/assets/bundles/web/app/widget/seal/img/ssl_certificate/de/150x150.png?20210322-140953" alt="SSL-Zertifikat" /></a> -->

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
      this.closeCounter()
      this.$axios(`${this.apiURL}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        this.get_ags()
        // https://www.dcat-ap.de/def/politicalGeocoding/municipalityKey/20210131.html
        
      })
      this.$q.loading.hide()
    },
    getWeatherbySearch(){
      this.$q.loading.show()
      this.closeCounter()
      this.$axios(`${this.apiURL}?q=${this.search}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        console.log('weathermap', this.weatherData)
        if (this.weatherData.name.includes("Regierungsbezirk")){ //Direktionsbezirk
          this.weatherData.name = this.weatherData.name.slice(17)
        } else if (this.weatherData.name == 'Wurzburg') {
          this.weatherData.name = 'Würzburg'
        } else if (this.weatherData.name == 'Munich') {
          this.weatherData.name = 'München'
        } else if (this.weatherData.name == 'Nuremberg') {
          this.weatherData.name = 'Nürnberg'
        }
        this.get_ags()
      })
      this.$q.loading.hide()
    },
    get_ags(){
      var ort = this.weatherData.name
      var ags = '';
      this.$axios.get('ags.txt').then(response=>{
        for (let index = 0; index < 5; index++) {
          ags = ags + response.data.charAt(response.data.indexOf(ort)-26 + index)    
        }
        this.gemeindezahl = ags
        console.log('gemeindezahl', this.gemeindezahl)
        this.getCoronaStats()
      })

    }, 
    getCoronaStats(){
      console.log('get c-data', this.gemeindezahl)

      this.$axios(`https://api.corona-zahlen.org/districts/${this.gemeindezahl}`).then(response=>{
        this.coronaData = response.data
        console.log('c-data', this.coronaData)
      })
    
    },
    closeInfo(){
      var x = document.getElementById("Info");
      x.style.display = "none"
    },
    closeCounter(){
      var x = document.getElementsByClassName("counterimg")[0]
      x.style.display = "none"
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
    overflow: hidden

  .getLocationBtn
    z-index: 10
  
  .topSearch
    z-index: 11
  
  .intro
    margin-top: -6rem

  .buymecoffee
    vertical-align: super
    color: #cfcfcf
    text-decoration: none
  
  .buymecoffee-img
    margin-right: 5px
  
  .donation
    margin-bottom: 8px

  .wrapper
    position: absolute
    width: 100%
    height: 100%
    overflow: hidden
    z-index: -1
  
  .textData
    margin-top: 0rem
  
  .meta-data
    display: contents
    font-size: 11px
    color: #cfcfcf
  
  .meta-data-div
    padding-bottom: 3rem
  
  .weatherData-name
    margin-bottom: 8px

  .counterimg
    position: absolute
    top: 50%
    left: 50%
    transform: translate(-50%, -7.5rem)
    z-index: 100
    opacity: 0.8
    display: block
  
  .box div
    position: absolute
    width: 60px
    height: 60px
    background-color: transparent
    border: 4px solid #efefef

  .box div:nth-child(1)
    top: 12%
    left: 42%
    animation: animate 10s linear infinite
  
  .box div:nth-child(2)
    top: 90%
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
