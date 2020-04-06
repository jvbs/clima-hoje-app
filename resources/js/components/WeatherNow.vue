<template>
  <div class="text-white mb-8">
    <div class="places-input text-gray-800">
      <input type="text" class="w-full" id="address-input">

      <p>Selecionado: <strong id="address-value">vazio</strong></p>
    </div>
    <div class="weather-container font-sans w-128 max-w-lg rounded-lg
    overflow-hidden bg-gray-400 text-gray-800 shadow-lg mt-4">
      <div class="current-weather flex items-center justify-between px-6 py-8">
        <div class="flex items-center">
          <div>
            <div class="text-6xl font-semibold">{{ currentTemperature.actualTemp }}°C</div>
            <div>Sensação térmica de {{ currentTemperature.feelsLike }}°C</div>
          </div>
          <div class="mx-5">
            <div class="font-semibold">{{ currentTemperature.weatherSummary }}</div>
            <div>{{ location.name }}</div>
          </div>
        </div>
        <div>
          <!-- Weather icon directly from the API -->
          <img :src="'http://openweathermap.org/img/wn/'+ currentTemperature.icon +'@2x.png'">
        </div>
      </div> <!-- end current weather -->

      <div class="future-weather text-sm bg-white text-gray-800 px-6 py-8 overflow-hidden">
        <div v-for="(day, index) in daily" :key="index" :class="{ 'mt-8': index > 0 }" class="flex items-center">
          <div class="w-1/6 text-lg">{{ toDayOfWeek(day.dt) }}</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>
              <img :src="'http://openweathermap.org/img/wn/'+ day.weather[0].icon +'@2x.png'" :style="{ width : '40px', height: '40px' }">
            </div>
            <div class="ml-3">{{ day.weather[0].main }} - {{ day.weather[0].description }}</div>
          </div>
          <div class="w-1/6 text-right">
            <div>Máx {{ Math.round(day.temp.max) }}°C</div>
            <div>Min {{ Math.round(day.temp.min) }}°C</div>
          </div>
        </div>
      </div> <!-- end future weather -->
    </div> <!-- end weather container -->
  </div>
</template>

<script>
export default {
  mounted() {
    this.fetchData()
    // connection with algolia API
    var placesAutocomplete = this.algoliaConnection()

    placesAutocomplete.on('change', (e) => {
      document.getElementById("address-value").textContent = `${e.suggestion.name}, ${e.suggestion.country}`

      this.location.name = e.suggestion.value
      this.location.lat = e.suggestion.latlng.lat
      this.location.lon = e.suggestion.latlng.lng
    })

    placesAutocomplete.on('clear', function(){
      document.getElementById("address-value").textContent = 'vazio'
    })
  },
  watch: {
    location: {
      handler(newValue, oldValue){
        this.fetchData()
      },
      deep: true
    }
  },
  data() {
    return {
      currentTemperature: {
        actualTemp: '',
        feelsLike: '',
        weatherSummary: '',
        weatherDesc: '',
        icon: ''
      },
      daily: [],
      location: {
        name: 'São Paulo, Brazil',
        lat: -23.5489,
        lon: -46.6388
      }
    }
  },
  methods: {
    fetchData(){
      fetch(`/api/weather?lat=${this.location.lat}&lon=${this.location.lon}`)
      .then(response => response.json())
      .then(data => {
        // console.log(data)
        this.currentTemperature.actualTemp = Math.round(data.current.temp)
        this.currentTemperature.feelsLike = Math.round(data.current.feels_like)
        this.currentTemperature.weatherSummary = data.current.weather[0].main
        this.currentTemperature.weatherDesc = data.current.weather[0].description
        this.currentTemperature.icon = data.current.weather[0].icon
        this.daily = data.daily
      })
    },
    toDayOfWeek(timestamp){
      const newDate = new Date(timestamp*1000)
      const daysInTheWeek = ['DOM', 'SEG', 'TER', 'QUA', 'QUI', 'SEX', 'SÁB']

      return daysInTheWeek[newDate.getDay()]
    },
    algoliaConnection(){
      var placesAutocomplete = places({
        appId: 'plP6H7587QMM',
        apiKey: '8597782607ca1f1a94c0b0929d5db518',
        container: document.getElementById("address-input")
      }).configure({
        type: 'city',
        aroundLatLngViaIP: false
      });

      return placesAutocomplete;
    }
  }
}
</script>
