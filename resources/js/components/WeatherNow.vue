<template>
  <div class="text-white mb-8">
    <div class="places-input text-gray-800">
      <input type="text" class="w-full">
    </div>
    <div class="weather-container font-sans w-128 max-w-lg rounded-lg
    overflow-hidden bg-gray-900 shadow-lg mt-4">
      <div class="current-weather flex items-center justify-between px-6 py-8">
        <div class="flex items-center">
          <div>
            <div class="text-6xl font-semibold">{{ currentTemperature.actualTemp }}°C</div>
            <div>Sensação de {{ currentTemperature.feelsLike }}°C</div>
          </div>
          <div class="mx-5">
            <div class="font-semibold">{{ currentTemperature.weatherSummary }}</div>
            <div>{{ currentTemperature.location }}</div>
          </div>
        </div>
        <div>icon</div>
      </div> <!-- end current weather -->

      <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
        <div class="flex items-center">
          <div class="w-1/6 text-lg text-gray-200">MON</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>icon</div>
            <div class="ml-3">Cloudy with a chance of showers.</div>
          </div>
          <div class="w-1/6 text-right">
            <div>5°C</div>
            <div>-2°C</div>
          </div>
        </div>
        <div class="flex items-center mt-8">
          <div class="w-1/6 text-lg text-gray-200">MON</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>icon</div>
            <div class="ml-3">Cloudy with a chance of showers.</div>
          </div>
          <div class="w-1/6 text-right">
            <div>5°C</div>
            <div>-2°C</div>
          </div>
        </div>
        <div class="flex items-center mt-8">
          <div class="w-1/6 text-lg text-gray-200">MON</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>icon</div>
            <div class="ml-3">Cloudy with a chance of showers.</div>
          </div>
          <div class="w-1/6 text-right">
            <div>5°C</div>
            <div>-2°C</div>
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
  },
  data() {
    return {
      currentTemperature: {
        actualTemp: '',
        feelsLike: '',
        minTemp: '',
        maxTemp: '',
        weatherSummary: '',
        weatherDesc: '',
        location: '',
        icon: ''
      },
      location: {
        name: 'Sao Paulo, Brazil',
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
        console.log(data)
        this.currentTemperature.actualTemp = Math.round(data.main.temp)
        this.currentTemperature.feelsLike = Math.round(data.main.feels_like)
        this.currentTemperature.minTemp = Math.round(data.main.temp_min)
        this.currentTemperature.maxTemp = Math.round(data.main.temp_max)
        this.currentTemperature.weatherSummary = data.weather[0].main
        this.currentTemperature.weatherDesc = data.weather[0].description
        this.currentTemperature.icon = data.weather[0].icon
        this.currentTemperature.location = data.name
      })
    }
  }
}
</script>
