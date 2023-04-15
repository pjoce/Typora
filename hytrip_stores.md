```js
// stores  city.js 
import { getCityAll } from '@/services'
import { defineStore } from 'pinia'

const useCityStore = defineStore('city', {
    state: () => ({
       allCities: {},
       currentCity: {
         cityName： '广州'
       }
    }),
    actions: {
       async fetchAllCitiesData() {
  	 	 const res = await getCityAll()
         this.allCities = res.data
       }
    }
})
export default  useCityStore


// home.js 

import { getHomeHotSuggests, getHomeCategories, getHomeHouselist } from '@/services'
import { defineStore } from 'pinia'

const useHomeStore = defineStore('home', {
    state: () => ({
        hotSuggests: [],
        categories: [],
        currentPage: 1,
        houselist: []
    }),
    actions: {
        async fetchHotSuggestData() {
            const res = await getHomeHotSuggests()
            this. hotSuggests = res.data
        }
    }
})
```

