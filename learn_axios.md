```js
// 发送基本request 请求
axios.request({
    url: 'http://123.207.32.32:8000/home/multidata',
    method: "get",
}).then(res => {
    console.log('res:', res.data)
})

//添加baseURL
const baseURL = 'http://123.207.32.32:8000'

//给axios 实例配置公共基础配置
axios.defaults.baseURL = baseURL
axios.defaults.timeout = 10000
axios.defaults.headers = {}


//创建axios的实例 
const instance1 = axios.create({
    baseURL: 'http://123.207.32.32:9001',
    timeout: 6000,
    headers: {}
})

instance1.get('/lyric', {
    params: {
      id: 500665346
    }
}).then(res => {
    console.log('res:', res.data)
})

//对实例配置拦截器
axios.interceptors.request.use((config) => {
		
})


```

