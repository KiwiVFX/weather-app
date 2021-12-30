<template>
  <q-page class="flex column" :class="bgClass">
   <div class="col q-pt-lg q-px-md">
     <q-input @keyup.enter="getWeatherBySearch(search)" dark v-model="search" placeholder="Search..." borderless maxlength="20" dense>
        <template v-slot:before>
          <q-icon name="my_location" @click="handleClick" />
        </template>

        <template v-slot:append>
          <q-icon v-if="search !== ''" name="close" @click="search = ''" class="cursor-pointer" />
          <q-icon name="search" @click="getWeatherBySearch(search)" />
        </template>

        <!-- <template v-slot:hint>
          Field hint
        </template> -->
      </q-input>
   </div>

   <template v-if="weatherData">
     
     <div class="col text-white text-center">
     <div class="text-h4 text-weight-light">
       {{weatherData.name}}
     </div>
     <div class="text-h6 text-weight-light">
       {{weatherData.weather[0].main}}
     </div>
     <div class="text-h1 text-weight-thin q-my-lg relative-position">
<span>{{Math.round(weatherData.main.temp)}}</span>
<span class="text-h1 relative-position degree">&deg;<span class="text-h2 relative-position degree celsius">C</span></span>
     </div>
     <div class="col text-center">
       <img :src="`http://openweathermap.org/img/wn/${ weatherData.weather[0].icon }@2x.png`">
       
     </div>

   
   </div>
     </template>
     <template v-else>
       <div class="col column text-center text-white">
         <div class="col text-h2 text-weight-thin">
         Geo Location<br>Weather
         </div>
         <q-btn class="col" flat @click="handleClick">
      <q-icon left size="3em" name="my_location" />
      <div>Locate me</div>
      
    </q-btn>
       </div>
     </template> 
   <div class="col skyline">
     </div>
  </q-page>
</template>

<script lang="ts">
// import { Todo, Meta } from 'components/models';
// import ExampleComponent from 'components/CompositionComponent.vue';
import { defineComponent, ref } from 'vue';
import { Platform } from 'quasar'
import { api } from 'boot/axios'
import {
  Loading,
  // Notify,
  // optional!, for example below
  // with custom spinner
  QSpinnerGears
} from 'quasar'
import Obj from '../types/object';


export default defineComponent({
  name: 'PageIndex',
  components: { },
  setup() {
    // const todos = ref<Todo[]>([
    //   {
    //     id: 1,
    //     content: 'ct1'
    //   },
    //   {
    //     id: 2,
    //     content: 'ct2'
    //   },
    //   {
    //     id: 3,
    //     content: 'ct3'
    //   },
    //   {
    //     id: 4,
    //     content: 'ct4'
    //   },
    //   {
    //     id: 5,
    //     content: 'ct5'
    //   }
    // ]);
    // const meta = ref<Meta>({
    //   totalCount: 1200
    // });
    // const data = ref(null)

    // const apiKey = '5bed5adf923966a50bcf176517289d55';
    // const apiURL = 'https://api.openweathermap.org/data/2.5/weather';
     const lat = ref<number>(0);
     const lon = ref<number>(0);
     const search = ref<string>('');
     const error = ref<string>('');
     const weatherData = ref<Obj>();

     const handleClick = () => {
       Loading.show({
  spinner: QSpinnerGears,
  // other props
})
if(Platform.is.electron) {
  //  console.log("I'm electron")
  return void api.get('https://freegeoip.app/json/')
      .then((response) => {
        // data.value = response.data
        // weatherData.value = response.data
        // let weatherData: unknown;
// console.log(response)
         // eslint-disable-next-line @typescript-eslint/no-unsafe-member-access
         var latitude = response.data.latitude as number
        // eslint-disable-next-line @typescript-eslint/no-unsafe-member-access
        var longetude = response.data.longitude as number
        // weatherData.value = response.data as Obj
        // Loading.hide()

         void api.get(`https://api.openweathermap.org/data/2.5/weather?lat=${ latitude }&lon=${ longetude }&appid=5bed5adf923966a50bcf176517289d55&units=metric`)
      .then((response) => {
        // data.value = response.data
        // weatherData.value = response.data
        // let weatherData: unknown;

        
        weatherData.value = response.data as Obj
        Loading.hide()

      })
    // console.log('weatherData: ', weatherData.value)
      })

} else {
  navigator.geolocation.getCurrentPosition(position => {
        // console.log('position is: ', position)
        lat.value = position.coords.latitude
        lon.value = position.coords.longitude
        // axios(`${ apiURL.value }?lat=${ lat.value }&lon=${ lon.value }&appid=${ apiKey.value }`)
        // console.log('this.lat: ', this.lat)
        // console.log('this.lon: ', this.lon)
        // this.getWeatherByCoords()
            void api.get(`https://api.openweathermap.org/data/2.5/weather?lat=${ lat.value }&lon=${ lon.value }&appid=5bed5adf923966a50bcf176517289d55&units=metric`)
      .then((response) => {
        // data.value = response.data
        // weatherData.value = response.data
        // let weatherData: unknown;

        
        weatherData.value = response.data as Obj
        Loading.hide()
    // console.log('weatherData: ', weatherData.value)
      })
       })
}
        


     
      // .catch(() => {
      //   $q.notify({
      //     color: 'negative',
      //     position: 'top',
      //     message: 'Loading failed',
      //     icon: 'report_problem'
      //   })
      // })

     

       
     }


     const getWeatherBySearch = (search: string) => {
       Loading.show({
  spinner: QSpinnerGears,
  // other props
})
// try {
 void api.get(`https://api.openweathermap.org/data/2.5/weather?q=${search}&appid=5bed5adf923966a50bcf176517289d55&units=metric`)
      .then((response) => {
        weatherData.value = response.data as Obj
        Loading.hide()
      })
      .catch(err => {
        // eslint-disable-next-line @typescript-eslint/no-unsafe-member-access
        console.log(err.message)
        // eslint-disable-next-line @typescript-eslint/no-unsafe-member-access
        error.value = err.message as string
        Loading.hide()
        // eslint-disable-next-line @typescript-eslint/no-unsafe-member-access
        // Notify.create({message: 'test'}
           
        
      })
      
// } catch (err) {
//         console.log(err)
// }
       
     
     }

     


    return { /*todos, meta,*/ error, lat, lon, search, weatherData, handleClick, getWeatherBySearch };
  },
   watch: {
    // whenever question changes, this function will run
    error: function (newError, oldError) {
      if(newError) {
        this.$q.notify({
          message: newError as string,
           color: 'red-5',
          textColor: 'white',
          icon: 'warning',
          progress: true,
        })
         
      } else {
        if(oldError) {
          this.error = ''
        }
      }
    }
  },
  computed: {
     bgClass () {
        // console.log("check bg class")
        if(this.weatherData) {
          // console.log("weather data is: ", this.weatherData)
       if (this.weatherData.weather[0].icon.endsWith('n')) {
         return 'bg-night'
       } else return 'bg-day'
        } else return ''
        
     }
  }
  // mounted(){
  //   console.log('lat is: ', this.lat);
  //   console.log('lon is: ', this.lon);
  //   console.log('search is: ', this.search);
  // },
//   methods: {
//     getLocation() {
//       navigator.geolocation.getCurrentPosition(position => {
//         console.log('position is: ', position)
//         this.lat = position.coords.latitude
//         this.lon = position.coords.longitude
//         console.log('this.lat: ', this.lat)
//         console.log('this.lon: ', this.lon)
//         this.getWeatherByCoords()
//       })
//     },
//     getWeatherByCoords() {
// this.$axios(`${ this.apiURL }?lat=${ this.lat }&lon=${ this.lon }&appid=${ this.apiKey }`)
//     }
  // }
});
</script>

<style lang="sass">

  .q-page
      background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e)
      &.bg-night
        background: linear-gradient(to bottom, #232526, #414345)
      &.bg-day
        background: linear-gradient(to bottom, #00b4db, #0083b0)

  .degree
     top: -10px

  .celsius
     top: -25px

  .skyline
    flex: 0 0 200px
    background: url(../assets/skyline.png)
    background-size: contain
    background-position: center bottom
</style>