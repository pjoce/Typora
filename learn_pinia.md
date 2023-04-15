```js
//user.js 

import { defineStore } from 'pinia'
const useUser = defineStore('user', {
    state: () => ({
       name: 'why',
       age: 18,
       level: 100
    })
})
export default useUser


// home.js 
import { defineStore } from 'pinia'
const useHome = defineStore('home', {
    state： () => ({
      banners: [],
      recommends: []
  }),
    actions: {
      async fetchHomeMultidata() {
      	const res = await fetch('http://123.207.32.32:8000/home/multidata')
        const data = await res.json()
        this.banners = data.data.banner.list
        this.recommends = data.data.recommend.list
      }
    }
})
export default useHome


// store counter.js 
// 定义关于counter的store

import { defineStore } from 'pinia'
import useUser from './user'

const useCounter = defineStore('counter', {
   state: ()=> ({
     counter: 99,
     friends: [
       {id: 11, name: 'xiaomin'},
       {id: 22, name: 'xiaohong'},
       {id: 33, name: 'xiaolv'}
     ]
   }),
    getters: {
        doubleCount(state) {
           ret
        }
    }
})


```

