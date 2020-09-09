# Vue项目

### 一、Promise

#### 1.1 Promise的基本使用

##### 	1.1.1 语法

​	new Promise( function(resolve, reject) {...} /* executor */  );

##### 	1.1.2 描述

​	一个Promise对象有一下几种状态

- *pending*：初始状态，既不是成功，也不是失败

- *fulfilled*：意味着操作成功完成
- *rejected*：意味着操作失败

promise对象的`then`方法绑定*fulfilled*状态，`catch`方法绑定*rejected*状态

##### 	1.2.3 创建*promise*

```javascript
const myFisrtPromise = new Promise((resolve, reject) => {
    // 做一些异步操作，最终会调用下面两者之一
    // resolve(someValues); // fulfilled
    // 或者
    // reject('failure reason'); // rejected
})
```

想要某个函数?拥有promise功能，只需让其返回一个promise即可。

```javascript
function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
};
```



#### 1.2 Promise的链式调用

因为 `Promise.prototype.then` 和 `Promise.prototype.catch` 方法返回promise 对象， 所以它们可以被链式调用。

![img](https://mdn.mozillademos.org/files/8633/promises.png)

#### 1.3 Promise的all方法

这个方法返回一个新的promise对象，该promise对象在iterable参数对象里所有的promise对象都成功的时候才会触发成功，一旦有任何一个iterable里面的promise对象失败则立即触发该promise对象的失败。这个新的promise对象在触发成功状态以后，会把一个包含iterable里所有promise返回值的数组作为成功回调的返回值，顺序跟iterable的顺序保持一致；如果这个新的promise对象触发了失败状态，它会把iterable里第一个触发失败的promise对象的错误信息作为它的失败错误信息。Promise.all方法常被用于处理多个promise对象的状态集合。

```javascript
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve, reject) => {
    setTimeout(resolve, 100, 'foo');
});
Promise.all([promise1, promise2, promise3]).then((value) => {
    console.log(value);
})
// out: Array [3, 42, 'foo']
```

语法：

`Promise.add(iterable)`

参数：

*iterable*：一个可迭代对象，如*Array*或*String*

返回值：

- 如果传入的参数是一个空的可迭代对象，则返回一个**已完成（already resolved）**状态的 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。
- 如果传入的参数不包含任何 `promise`，则返回一个**异步完成（asynchronously resolved）** [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。注意：Google Chrome 58 在这种情况下返回一个**已完成（already resolved）**状态的 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。
- 其它情况下返回一个**处理中（pending）**的[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。这个返回的 `promise` 之后会在所有的 `promise` 都完成或有一个 `promise` 失败时**异步**地变为完成或失败。 见下方关于“Promise.all 的异步或同步”示例。返回值将会按照参数内的 `promise` 顺序排列，而不是由调用 `promise` 的完成顺序决定。



### 二、Vuex

#### 2.1 什么是状态管理

这个状态自管理应用包含以下几个部分：

- **state**，驱动应用的数据源；
- **view**，以声明方式将 **state** 映射到视图；
- **actions**，响应在 **view** 上的用户输入导致的状态变化。

![img](https://vuex.vuejs.org/flow.png)

*Vuex*的状态管理：

![vuex](https://vuex.vuejs.org/vuex.png)



#### 2.2 Vuex的基本使用

##### 2.2.1 安装

`npm install vuex --save`

##### 2.2.2 配置

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vue.Store({
    state: {
        count: 0
    },
    mutation: {
        increment(state) {
            state.count++;
        }
    }
})

export default store
```

##### 2.2.3 使用

可以通过使用`store.commit`来获取对象状态，以及通过`store.commit`方法触发状态变更

```javascript
store.commit('increment')

console.log(store.state.count) // -> 1
```

为了在 Vue 组件中访问 `this.$store` property，你需要为 Vue 实例提供创建好的 store。Vuex 提供了一个从根组件向所有子组件，以 `store` 选项的方式“注入”该 store 的机制：

```javascript
// ES6下可以简写
new Vue({
    el: '#app',
    // store: store
    store
})

// 现在我们提交一个变更
method: {
    increment() {
        this.$store.commit('increment');
        console.log('this.$store.state.count') // -> 1
    }
}
```



#### 2.3 核心概念

##### 2.3.1 单一状态树

Vuex 使用**单一状态树**——是的，用一个对象就包含了全部的应用层级状态。至此它便作为一个“唯一数据源 ([SSOT](https://en.wikipedia.org/wiki/Single_source_of_truth))”而存在。这也意味着，每个应用将仅仅包含一个 store 实例。单一状态树让我们能够直接地定位任一特定的状态片段，在调试的过程中也能轻易地取得整个当前应用状态的快照。

**在Vue组件中获得Vuex状态**

```javascript
const app = new Vue({
    el: '#app',
    // 把store对象提供给‘store’选项，这可以把store的实例注入到所有的子组件
    store,
    components: {
        counter
    },
    template: `
		<div class='app'
			<counter></counter>
		</div>
	`
})

const Counter = {
    template: `<div>{{ count }}</div>`,
    computed: {
        count() {
            return this.$store.count
        }
    }
}
```

*mapState*辅助函数：当一个组件需要获取多个状态的时候，将这些状态都申明为计算属性会有些重复和冗余；为了解决这个问题，我们可以使用*mapState*辅助函数帮助我们生成计算属性

```javascript
// 在单独构建的版本中辅助函数为 Vuex.mapState
import {mapState} from 'vuex'
export default {
    // ...
    computed: mapState({
        count: state => state.count,
        
        // 传字符串参数‘count’ 等同于 `state => state.count`
        countAlias: 'count',
        
        countPlusLocalState(state) {
            return state.count + this.localCount
        }
    })
}
```

##### 2.3.2 getter

```javascript
// 对于从store中的state中派生出的一些状态 
computed: {
    doneTodosCount() {
        return this.$store.state.todos.filter(todo => todo.done).length
    }
}
// 如果有多个组件需要用到此属性，无论是复制此函数还是抽取为共享函数多处导入都不是很理想
```

**vue允许我们在store中定义getter（可以认为是store的计算属性）；和计算属性一样，getter的返回值会根据它的依赖被缓存起来，且只有它的依赖发生了改变才会被重新计算**

```javascript
const store = new Vuex.Store({
    state: {
        todos: [
            {id: 1, text: '...', done: true},
            {id: 2, text: '...', done: true}
        ]
    },
    getters: {
        doneTodos: state => {
    		return state.todos.filter(todo => todo.done)
  		}
    }
})
```

**通过属性访问**

Getter会暴露为`store.getters`对象，你可以以属性的形式访问这些值

```javascript
store.getters.doneTodos // [{id: 1, text: '...', done: true}]
```

Getter 也可以接受其他 getter 作为第二个参数：

```javascript
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}

store.getters.doneTodosCount // -> 1
```

我们可以很容易地在任何组件中使用它：

```javascript
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}

// 注意，getter 在通过属性访问时是作为 Vue 的响应式系统的一部分缓存其中的。
```

##### 2.3.3 mutation

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```javascript
const store = new Vuex.Store({
    state: {
        count: 1
    },
    mutations: {
        increment(state) {
            // 变更状态
            state.count++;
        }
    }
})
```

你不能直接调用一个 mutation handler。这个选项更像是事件注册：“当触发一个类型为 `increment` 的 mutation 时，调用此函数。”要唤醒一个 mutation handler，你需要以相应的 type 调用 **store.commit** 方法：

```javascript
store.commit('increment')
```

**提交载荷  payload**

```javascript
// 可以向store.commit传入额外的参数 即mutation的载荷（payload）
// ....
mutation: {
    increment(state, payload) {
        state.count += payload
    }
}

store.commit('increment', 10)

// payload可以是一个对象
mutation: {
    increment(state, payload) {
        state.count += payload.amount
    }
}

store.commit('increment', {
    amount: 10
})

// 对象风格提交方式
store.commit({
    type: 'increment',
    amount: 10
})
```

**mutation必须是同步函数**：

一条重要的原则就是要记住 **mutation 必须是同步函数**。为什么？请参考下面的例子：

```javascript
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
```

现在想象，我们正在 debug 一个 app 并且观察 devtool 中的 mutation 日志。每一条 mutation 被记录，devtools 都需要捕捉到前一状态和后一状态的快照。然而，在上面的例子中 mutation 中的异步函数中的回调让这不可能完成：因为当 mutation 触发的时候，回调函数还没有被调用，devtools 不知道什么时候回调函数实际上被调用——实质上任何在回调函数中进行的状态的改变都是不可追踪的。

##### 2.3.4 actions

- *action*提交的是mutation，而不是直接变更状态
- *action*可以包含任意异步操作

```javascript
const store = new Vuex.Store({
    state: {
        count: 0
    },
    mutation: {
        increment(state) {
            state.count++;
        }
    },
    action: {
        increment(context) {
            context.commit('increment')
        }
    }
})

// 分发action
store.dispatch('increment')

action: {
    incrementAsync({commit}) {
        setTimeout(() => {
            commit('increment')
        }, 1000)
    }
}

// 同样支持载荷和对象的形式
store.dispatch('incrementAsync', {
    amount: 10
})

store.dispatch({
    type: 'incrementAsync',
    amount: 10
})
```

涉及调用异步API和分发多重mutation：

```javascript
action: {
    checkout({commit, state}, products) {
        // 把当前购物车的物品备份起来
        const savedCartItems = [...state.cart.added]
        // 发出结账请求，然然后乐观地清空购物车
        commit(types.CHECKOUT_REQUEST)
        // 购物API接受一个成功回调和一个失败回调
        shop.buyProducts(products, () => {
            commit(types.CHECKOUT_SUCCESS)
        }, () => {
            commit(types.CHECKOUT_FAILURE, savedCartItems)
        })
    }
}
```

**在组件中分发action**

你在组件中使用 `this.$store.dispatch('xxx')` 分发 action，或者使用 `mapActions` 辅助函数将组件的 methods 映射为 `store.dispatch` 调用（需要先在根节点注入 `store`）：

```javascript
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```

##### 2.3.5 modules

*为了解决store对象在应用变得复杂的时候，自身臃肿的问题*

Vuex允许我们将Store分割成**模块（module）**；每个模块都有自己的state，mutation，action，getter，甚至是嵌套子模块-----自上而下进行同样的分割：

```javascript
const moudleA = {
    state: () => {},
    mutation: {},
    actions: {},
    getters: {}
}
```



#### 2.4 目录组织方式



### 三、网路请求封装

#### 3.1 网络请求方式的选择

- jquery.ajax
- axios.get axios.post

#### 3.2 axios的基本使用

##### 3.2.1 创建实例

axios.create([config])

```javascript
const instance = axios.create({
    baseURL: 'https://some-domain.com/api/',
    timeout: 1000,
    headers: {'X-Custom-Header': 'foobar'}
})
```

##### 3.2.2 实例方法

- axios#request(config)
- axios#get(url[, config])
- axios#delete(url[, config])
- axios.head(url[, config])
- axios.options(url[, config])
- axios.post(url[, data[, config]])
- axios.put(url[, data[, config]])
- axios.patch(url[, data[, config]])

##### 3.2.3 请求配置

这些是创建请求时可以用的配置选项。只有 `url` 是必需的。如果没有指定 `method`，请求将默认使用 `get` 方法。

```javascript
{
    // 'url'是用于请求的服务器URL
    url: '/user',
    // 'method'是创建请求时使用的方法
    method: 'get',// default
    // 'baseURL'将自动加在'url'前面，除非'url'是一个绝对URL
    // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL    
    baseURL: 'https://some-domain.com/api/',
     // `transformRequest` 允许在向服务器发送前，修改请求数据
        
     // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
     // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
    transformRequest: [function (data, headers) {
     // 对 data 进行任意转换处理
       return data;
    }],

     // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
    transformResponse: [function (data) {
        // 对 data 进行任意转换处理
      return data;
    }],
    
    // 'headers'是即将被发送的自定义请求头
    headers: {'X-Request-With': 'XMLHttpRequest'},
    
    // 'params'是即将与请求一起发送的URL参数
    // 必须是一个无格式对象（plain object）或URLSearchParams对象
    params: {
        ID: 123456
    },
    // ...    
}
```

#### 3.3 axios的相关配置

##### 3.3.1 全局axios默认值

```javascript
axios.defaults.baseURL='https://api.example.com';
axios.defaults.headers.common['Authorization']=AUTH_TOKEN;
axios.defaults.headers.post['Content-Type']='application/x-www-form-urlencoded';
```

##### 3.3.2 配置的优先顺序

配置会以一个优先顺序进行合并；这个顺序是：在`lib/defaults.js`找到的库的默认值，然后是实例的`defaults`属性，最后是请求的`config`参数。后者优先前者：

```javascript
// 使用由库提供的配置的默认值来创建实例
// 此时超时配置的默认是‘0’
var instance = axios.create();

// 复写库的超时默认值
instance.defaults.timeout=2500;

// 为已知需要花费很长时间的请求复写超时设置
instance.get('/longRequest', {
    timeout: 5000
})
```

##### 3.3.3 拦截器

在请求或响应被`then`或`catch`处理前拦截

```javascript
// 添加请求拦截器
axios.interceptors.request.use(function(config){
    // 在发送请求之前做些什么
    return config;
}, function(error){
    // 对请求错误做些什么
    return Promise.reject(error);
})

// 添加响应拦截器
axios.interceptors.response.use(function(response){
    // 对响应数据做些什么
    return response;
}, function(error){
    // 对响应错误做些什么
    return Promise.reject(error);
})
```

如果你想在稍后移除拦截器：

```javascript
axios.interceptors.request.eject(myInterceptors)
```

#### 3.4 axios的封装

项目在开发过程中考虑到引入第三方框架是带来的风险（第三方框架不再维护，需要寻找替代方案），这个时候不适于在项目中直接引用第三方框架；而是封装一层代理引用：

```javascript
import axios from 'axios'

export function request(config) {
  // 1.创建axios的实例
  const instance = axios.create({
    baseURL: 'http://123.207.32.32:8000',
    timeout: 5000
  })

  // 2.axios拦截器
  instance.interceptors.request.use(config => {
    // 1.config中的一些数据不符合交易需求
    // 2.在请求过程是展示一个请求的图标
    // 3.某些网络请求（比如登录--token）， 必须携带一些特殊的信息
    // console.log(config);
    // 通过放行
    return config
  }, error => {
    console.log(error);
  })

  // 相应拦截
  instance.interceptors.response.use(result => {
    console.log(result);
    return result.data
  }, error => {
    console.log(error);
  })

  // 3.发送真正的网络请求
  return instance(config)
}

// 引用代理
import {request} from "./request";

export function getHomeMultidata() {
  return request({
    url: '/home/multidata',
    params: {
      'userId': 'a123456'
    },
    timeout: 2000,
    auth: {
      username: 'melody',
      password: '123456'
    }
  })
}
```



### 四、项目开发

#### 4.1 划分目录结构



#### 4.2 引用两个css文件（base.css和normalize.css）

#### 4.3 vue.config.js和.editorconfig

#### 4.4 项目的模块划分：tabber -> 路由映射关系

#### 4.5 首页开发

- navbar的封装
- 网络数据的请求
- 轮播图
- 推荐信息