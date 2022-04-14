Promise

API（应用程序接口）：是基于编程语言构建的结构，使开发人员更容易地创建复杂的功能。它们抽象了复杂的代码，并提供一些简单的接口规则直接使用。
  
  Promise是一个类：
  
1）类是一个特殊的函数

2）类方法：

• Promise.all  在任何情况下，Promise.all 返回的 promise 的完成状态的结果都是一个数组，它包含所有的传入迭代参数对象的值（也包括非 promise 值）。

• Promise.race  （返回一个 promise，一旦迭代器中的某个promise解决或拒绝，返回的 promise就会解决或拒绝。）

• Promise.reject  （返回一个带有拒绝原因的Promise对象。）

• Promise.resolve  （方法返回一个以给定值解析后的Promise 对象。如果这个值是一个 promise ，那么将返回这个 promise ；如果这个值是thenable（即带有"then" 方法），返回的promise会“跟随”这个thenable的对象，采用它的最终状态；否则返回的promise将以此值完成。此函数将类promise对象的多层嵌套展平。）

3）对象属性：
• Promise.prototype.then  （最多需要有两个参数：Promise 的成功和失败情况的回调函数。）

• Promise.prototype.finally（不管是对是错，都会执行指定的回调函数）

• Promise.prototype.catch  （可以用于promise组合中的错误处理。）

4）对象内部属性：
• state = pending/fullfilled/rejected

Promise/A+规范：

• 英文版： https://promisesaplus.com/

• 中文翻译版： https://www.ituring.com.cn/article/66566

更多用法：

1) Promise.race：得到一个最先resolve的结果
```
const getWeather = city => new Promise((resolve, reject) => {
  let xhr = new XMLHttpRequest()
  xhr.open('GET', 'http://rap2api.taobao.org/app/mock/244238/getWeather?city='+city, true)
  xhr.onload = () => resolve(JSON.parse(xhr.responseText))
  xhr.send()
})

let p1 = getWeather ('北京')
let p2 = getWeather ('上海')
let p3 = getWeather ('杭州')

Promise.race([p1, p2, p3]).then(data => { // data指数组[p1, p2, p3]谁最先得到结果 就得到谁
  console.log(data)
})
```

2) Promise.all：把全部resolve的结果放进一个数组
```
const getWeather = city => new Promise((resolve, reject) => {
  let xhr = new XMLHttpRequest()
  xhr.open('GET', 'http://rap2api.taobao.org/app/mock/244238/getWeather?city='+city, true)
  xhr.onload = () => resolve(JSON.parse(xhr.responseText))
  xhr.send()
})

let p1 = getWeather ('北京')
let p2 = getWeather ('上海')
let p3 = getWeather ('杭州')

Promise.all([p1, p2, p3]).then(data => { // data指数组[p1, p2, p3]各自的数据，汇总所有的结果一次得出
  console.log(data)
})
```

async/await

使用方法：

1）await需要在Promise对象之前

2）await只在async函数内有效
```
const getWeather = city => new Promise((resolve, reject) => {
  let xhr = new XMLHttpRequest()
  xhr.open('GET', 'http://rap2api.taobao.org/app/mock/244238/getWeather?city='+city, true)
  xhr.onload = () => resolve(JSON.parse(xhr.responseText))
  xhr.send()
})

async function start() {
  let weather1 = await getWeather('北京')
  console.log('北京:' + weather1.weather)

  let weather2 = await getWeather('杭州')
  console.log('杭州:' + weather2.weather)
} // 串联的写法，先执行weather1，而后执行我weather2 

start().catch(err=>console.log(err))
```

简易Promise：
```
class Promise2 {
  succeed = null
  fail = null
  state = 'pending' 

  resolve(result) {
      setTimeout(() => {
          this.state = 'fulfilled' 
          this.succeed(result)
      })
  }

  reject(reason) {
      setTimeout(() => {
          this.state = 'rejected' 
          this.fail(reason)
      })
  }

  constructor(fn) {
      fn(this.resolve.bind(this), this.reject.bind(this))
  }

  then(succeed, fail) {
    this.succeed = succeed
    this.fail = fail
  }
}

new Promise2((resolve, reject) => {
  console.log('fn run...')
  setTimeout(() => {
      if (Math.random() > 0.5) {
          resolve(100)
          } else {
          reject('失败')
          }
  }, 1000)
}).then(n => {
  console.log(n)
}, reason => {
  console.log(reason)
})
```

