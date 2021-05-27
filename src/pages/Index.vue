<template>
  <q-page class="flex column topSearch" :class="bgClass">

    <location-check />

    <div class="col q-pt-lg q-px-md">
      <q-input
        @keyup.enter="getWeatherbySearch"
        bottom-slots
        v-model="search"
        placeholder="Suche"
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
            style="margin-right: 5px"
            dense
            flat
            icon="search" />

          <q-btn
            round
            dense
            flat
            icon="more_vert" >
            <q-menu>
                <div class="row no-wrap q-pa-md side-menu" style="text-align: end;">
                  <div class="column">
                    <div class="text-h6 q-mb-md" style="margin-bottom: 1.5rem">Einstellungen</div>
                    <q-item-section
                      @click="gotoDatenschutz"
                      clickable
                      style="cursor: pointer;">
                     Datenschutz
                    </q-item-section>
                    <q-item-section
                      @click="gotoImpressum"
                      clickable
                      style="cursor: pointer;">
                     Impressum
                    </q-item-section>
                    <q-toggle 
                      @input="update_permission"
                      id="#dbpermission"
                      class="dbperm"
                      v-model="save_localbase" 
                      style="margin-top: 0.25rem;"
                      label="Suche speichern (beta)" />
                    <!-- <q-item-section style="margin-top: 1rem;">Impressum</q-item-section> -->
                  </div>
                </div>
            </q-menu>

          </q-btn>
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
        <template v-if="coronaData.data[gemeindezahl].weekIncidence < 35">
          <div class="text-h1 text-weight-light q-my-lg relative-position" style="color: green">
            <span>{{ Math.round(coronaData.data[gemeindezahl].weekIncidence)}}</span> 
          </div>
        </template>
        <template v-else-if="coronaData.data[gemeindezahl].weekIncidence > 50 && coronaData.data[gemeindezahl].weekIncidence < 80">
          <div class="text-h1 text-weight-light q-my-lg relative-position" style="color: orange">
            <span>{{ Math.round(coronaData.data[gemeindezahl].weekIncidence)}}</span> 
          </div>
        </template>
        <template v-else>
          <div class="text-h1 text-weight-light q-my-lg relative-position" style="color: red">
            <span>{{ Math.round(coronaData.data[gemeindezahl].weekIncidence)}}</span> 
          </div>
        </template>

        <div class="text-h7 text-weight-light">
          Letzten 3 Tage
        </div>
        <template v-if="threedayIncidence">
          <div class="text-h3 text-weight-light q-my-sm relative-position" style="color: red">
            <span>
              {{ 
                Math.round(threedayIncidence)
              }}
              </span> 
          </div>
        </template>
        <template v-else>
          <div class="text-h3 text-weight-light q-my-sm relative-position" style="color: red">
            <q-icon name="help_outline" />
          </div>
        </template>
      </div>


      <div class="meta-data text-center">
        <span><a class="meta-data-div meta-data" href="https://www.rki.de/DE/Content/InfAZ/N/Neuartiges_Coronavirus/Fallzahlen.html">Source: {{ coronaData.meta.source }}</a></span>
        <span>Last Update: {{ coronaData.meta.lastUpdate.split(".")[0] }}</span>
        <span class="meta-data-div"><a class="meta-data-div meta-data" href="https://api.corona-zahlen.org/docs/"> Data provided by: {{ coronaData.meta.contact.split("(")[0]  }}</a></span>
        <span class="donation">
          <img class="buymecoffee-img" src="buymeacoffee.svg" height="23px" />
          <a class="buymecoffee"  href="https://www.buymeacoffee.com/StudioSchmudio"> Buy us a coffee</a>
        </span> 
        
      </div>

    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h3 text-weight-thin intro">
          Wie hoch ist meine <br> Inzidenz?
        </div>
          <q-btn
            @click="getLocation"
            class="col getLocationBtn"
            flat>
            <q-icon left size="3em" name="my_location" />
            <div>Finde meinen Ort</div>
          </q-btn>
      </div> 
    </template>

    <bg-anim />

  </q-page>
</template>

<script>
import Localbase from 'localbase'

let db = new Localbase('db')

export default {
  name: 'PageIndex',
  data(){
    return{
      search: '',
      weatherData: null,
      coronaData: null,
      weekIncidence: '',
      gemeindezahl: null,
      threedayIncidence: null,
      maxItems: null,
      firstVal: null,
      secondVal: null,
      thirdVal: null,
      dbunique: false,
      dbindex: null,
      lat: null,
      lon: null,
      save_localbase: false,
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
  components: {
    'location-check': require('components/LocationCheck.vue').default,
    'bg-anim': require('components/BgAnimation.vue').default

  },
  methods: {
    initdb(){
      db.collection('permissions').get().then(resp =>{

        if (resp.length > 0) {
          // console.log("db already initialized")
          db.collection('permissions').doc({ id: 1 }).get().then(getPermissionValue => {
            this.save_localbase = getPermissionValue.permission           
          })

        } else {
          db.collection('permissions').set([
            {
              id: 1,
              permission: false,
            }
          ])
        }
      })
    },
    checkPermission(){

    },
    gotoDatenschutz(){
      this.$router.push("/datenschutz").then(() => this.$router.go())

    },
    gotoImpressum(){
      this.$router.push("/impressum").then(() => this.$router.go())
    },
    getLocation(){
      this.$q.loading.show()
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.showPosition, this.showError)
      } else {
        alert("Geolocation is not supported by this browser.")
      }
    },
    showPosition(position){
      this.lat= position.coords.latitude
      this.lon= position.coords.longitude
      this.getWeatherByCoords()
    },
    showError(error){
      this.$q.loading.hide()
      switch(error.code) {
        case error.PERMISSION_DENIED:
          var x = document.getElementsByClassName("turn-on-location")[0]
          x.style.display = "flex"
          break;
        case error.POSITION_UNAVAILABLE:
          alert("Location information is unavailable.")
          break;
        case error.TIMEOUT:
          alert("The request to get user location timed out.")
          break;
        case error.UNKNOWN_ERROR:
          alert("An unknown error occurred.")
          break;
      }
    },
    getWeatherByCoords(){
      this.$q.loading.show()
      // this.closeCounter()
      this.$axios(`${this.apiURL}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        this.get_ags()
        // console.log("weather_data::", this.weatherData)
        // https://www.dcat-ap.de/def/politicalGeocoding/municipalityKey/20210131.html
        
      })
      this.$q.loading.hide()
    },
    getWeatherbySearch(){
      this.$q.loading.show()
      // this.closeCounter()
      this.$axios(`${this.apiURL}?q=${this.search}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        // console.log('weathermap', this.weatherData)
        if (this.weatherData.name.includes("Regierungsbezirk")){ 
          this.weatherData.name = this.weatherData.name.slice(17)
        } else if (this.weatherData.name == 'Wurzburg') {
          this.weatherData.name = 'Würzburg'
        } else if (this.weatherData.name == 'Cologne') {
          this.weatherData.name = 'Köln'
        } else if (this.weatherData.name == 'Munich') {
          this.weatherData.name = 'München'
        } else if (this.weatherData.name == 'Nuremberg') {
          this.weatherData.name = 'Nürnberg'
        } else if (this.weatherData.name == 'Hanover') {
          this.weatherData.name = 'Hannover'
        } else if (this.weatherData.name == 'Hamelin') {
          this.weatherData.name = 'Hameln'
        } else if (this.weatherData.name.includes("Direktionsbezirk")) {
          this.weatherData.name = this.weatherData.name.slice(17)
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
        // console.log('gemeindezahl', this.gemeindezahl)
        this.getCoronaStats()
      })

    }, 
    getCoronaStats(){
      // console.log('get c-data', this.gemeindezahl)

      this.$axios(`https://api.corona-zahlen.org/districts/${this.gemeindezahl}`).then(response=>{
        this.coronaData = response.data
        this.calcLastThreeDays()
        // console.log('c-data', this.coronaData)
      })
    
    },localbase(){
      
      if (this.save_localbase == true){
        var today = new Date();
        var dd = String(today.getDate()).padStart(2, '0');
        var mm = String(today.getMonth() + 1).padStart(2, '0'); //January is 0!
        var yyyy = today.getFullYear();

        today = mm + '/' + dd + '/' + yyyy;

      // check if city already exists in db
        db.collection('3day'+this.weatherData.name).get().then(checkdb => {
          for (let index = 0; index < checkdb.length; index++) {
            this.maxItems=checkdb.length
            if (checkdb[index].timestamp == today ){
              // console.log("item already exists")
              this.dbunique = false
              break
            } else {
              this.dbunique = true
              this.dbindex = index
            }
          }
          
          
          if(checkdb.length == 0){
            this.dbunique = true
            this.dbindex = 0
          }


          if (this.dbunique == true){

            db.collection('3day'+this.weatherData.name).add({
              id: this.dbindex + 1,
              name: this.weatherData.name,
              incidence: Math.round(this.coronaData),
              timestamp: today
            })
            this.maxItems ++
          }

          this.calcLastThreeDays()


        })
      }
      
    },
    calcLastThreeDays(){    
      this.$axios(`https://api.corona-zahlen.org/districts/history/frozen-incidence/3`).then(response=>{  
        try {

          this.firstVal = response.data.data[this.gemeindezahl].history[0].weekIncidence

        } catch (err) {

          // error handling

        }
        
        
        try {

          this.secondVal = response.data.data[this.gemeindezahl].history[1].weekIncidence

        } catch (err) {

          // error handling

        }
        
        try {

          this.thirdVal = response.data.data[this.gemeindezahl].history[2].weekIncidence

        } catch (err) {

          // error handling

        }
        
        this.threedayIncidence = (this.firstVal + this.secondVal + this.thirdVal) /3
        // console.log(typeof(this.gemeindezahl))
        // console.log(this.firstVal["06411"].history[1].weekIncidence)
        // console.log(response.data.data)

        // console.log(response.data.data[this.gemeindezahl].history[0].weekIncidence)
        // this.threedayIncidence  = response.data



      })
    },
    closeInfo(){
      var x = document.getElementById("Info");
      x.style.display = "none"
    },
    update_permission(){
      // console.log("triggered")
      if (this.save_localbase == true){
        db.collection('permissions').set([
          {
            id: 1,
            permission: true,
          }
        ])
      } else {
        db.collection('permissions').set([
          {
            id: 1,
            permission: false,
          }
        ])
      }

    }

  },
  created () {
    this.initdb()
    this.checkPermission()
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

  body
    margin: 0
    padding: 0
    overflow: hidden

  body.desktop .q-focus-helper
    position: inherit
    

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
  
  .textData
    margin-top: -1rem

  .turn-on-location
    display: none
  
  .q-menu
    background: #0a0a0abf
    color: #dfdfdf
  
  .q-toggle__inner--truthy
    color: #a03f6c
  
  .meta-data
    display: contents
    font-size: 10px
    color: #cfcfcf
  
  .meta-data-div
    padding-bottom: 2rem
    text-decoration: none
  
  .weatherData-name
    margin-bottom: 8px


</style>
