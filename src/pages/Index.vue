<template>
  <q-page class="flex column topSearch" :class="bgClass">
    <q-banner inline-actions class="text-white bg-red turn-on-location" @click="TurnOnLocation">
      Bitte aktivieren Sie zuerst die Standortfreigabe auf Ihrem Gerät.
      <template v-slot:action>
        <q-btn flat color="white" label="Got it" />
      </template>
    </q-banner>

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
            dense
            flat
            icon="search" />
          <q-btn
            round
            dense
            flat
            icon="more_vert" />
            <q-menu>
                <div class="row no-wrap q-pa-md side-menu" style="text-align: end;">
                  <div class="column">
                    <div class="text-h6 q-mb-md">Einstellungen</div>
                    <q-toggle 
                      @input="update_permission"
                      id="#dbpermission"
                      class="dbperm"
                      v-model="save_localbase" 
                      label="Datenspeichern" />
                    <q-item-section style="margin-top: 1rem;">Impressum</q-item-section>
                    <q-item-section>Datenschutz</q-item-section>

                  </div>
                </div>
              </q-menu>
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

        <div class="text-h7 text-weight-light">
          Letzten 3 Tage
        </div>
        <template v-if="threedayIncidence">
          <div class="text-h3 text-weight-light q-my-sm relative-position" style="color: red">
            <span>{{ Math.round(threedayIncidence)}}</span> 
          </div>
        </template>
        <template v-else>
          <div class="text-h3 text-weight-light q-my-sm relative-position" style="color: red">
            <q-icon name="help_outline" />
          </div>
        </template>




      </div>
      <div class="meta-data text-center">
        <span>Source: {{ coronaData.meta.source }}</span>
        <span>Last Update: {{ coronaData.meta.lastUpdate.split(".")[0] }}</span>
        <span class="meta-data-div">Data provided by: {{ coronaData.meta.contact.split("(")[0]  }}</span>
        <span class="donation">
          <img class="buymecoffee-img" src="buymeacoffee.svg" height="23px" />
          <a class="buymecoffee"  href="https://www.buymeacoffee.com/StudioSchmudio"> Buy us a coffee</a>
        </span> 
        
      </div>
      <!-- <div class="col text-center">
        <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
      </div> -->

    </template>

    <template v-else>
      <!-- <a href='https://www.symptoma.ro/'>Căutare de informații medicale</a> -->
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

   <v-color-picker
      dot-size="25"
      hide-inputs
      swatches-max-height="100"
    ></v-color-picker>

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
  methods: {
    initdb(){
      db.collection('permissions').get().then(resp =>{

        if (resp.length > 0) {
          console.log("db already initialized")
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
        // https://www.dcat-ap.de/def/politicalGeocoding/municipalityKey/20210131.html
        
      })
      this.$q.loading.hide()
    },
    getWeatherbySearch(){
      this.$q.loading.show()
      // this.closeCounter()
      this.$axios(`${this.apiURL}?q=${this.search}&appid=${this.apiKey}&units=metric`).then(response=>{
        this.weatherData = response.data
        console.log('weathermap', this.weatherData)
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
        console.log('gemeindezahl', this.gemeindezahl)
        this.getCoronaStats()
      })

    }, 
    getCoronaStats(){
      console.log('get c-data', this.gemeindezahl)

      this.$axios(`https://api.corona-zahlen.org/districts/${this.gemeindezahl}`).then(response=>{
        this.coronaData = response.data
        this.localbase()
        console.log('c-data', this.coronaData)
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
              console.log("item already exists")
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


          console.log(this.dbunique == true)
          if (this.dbunique == true){

            db.collection('3day'+this.weatherData.name).add({
              id: this.dbindex + 1,
              name: this.weatherData.name,
              incidence: Math.round(this.coronaData.data[this.gemeindezahl].weekIncidence),
              timestamp: today
            })
            this.maxItems ++
          }

          this.calcLastThreeDays()


        })
      }
      
    },
    calcLastThreeDays(){
      db.collection('3day'+this.weatherData.name).get().then(checkdb => {
        this.firstVal = checkdb[this.maxItems - 3].incidence
        this.secondVal = checkdb[this.maxItems - 2].incidence
        this.thirdVal = checkdb[this.maxItems-1].incidence


        this.threedayIncidence = (this.firstVal + this.secondVal + this.thirdVal) /3
        

        console.log(this.thirdVal)
      }).catch(err => console.log(err))
    },
    TurnOnLocation(){
      var x = document.getElementsByClassName("turn-on-location")[0]
      x.style.display = "none"
    },
    closeInfo(){
      var x = document.getElementById("Info");
      x.style.display = "none"
    },
    closeCounter(){
      var x = document.getElementsByClassName("counterimg")[0]
      x.style.display = "none"
    },
    update_permission(){
      console.log("triggered")
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
            permission: true,
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
