```js
import { createRouter, createWebHashHistory } from 'vue-router'

const router = createRouter({
   history: createWebHashHistory(),
   routes: [
    {
      path: '/',
      redirct: '/home'
    },
     {
      path: '/home',
      component: () => import(/* webpackChunkName: "home" */ '@/views/Home.vue'),
      meta: {
         index: 1
      }
     },
     {
       path: '/login',
       name: 'login',
       component: () => import(/* webpackChunkName: 'about' */'@.views/About.vue'),
       meta: {
           index: 2
       }
     }
   ]
})
```

