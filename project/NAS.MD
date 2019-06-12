# NAS
## 1. 简介
谷神星NAS项目，用于远程控制服务器。后端：Node，前端Vue。

## 2. 项目模块
### 2.1 登录模块
主要用于用户验证，与前端有关的为全局路由守卫。逻辑选择
#### 1. 每次页面重载都会返回登录界面
```
const isLogin = false
router.beforeEnter((to, from, next) => {
  if (isLogin) {
    next()
  } else {
    next('/login')
  }
})
// 登录
const handleLogin = (username, password) => {
  login(username, password).then(_ => {
    isLogin = true
    router.push('/index')
  }).catch(err => {
    // dosomething
  })
}
```

#### 2. 客户端本地保存username，password
以localStorage为例

```
// 不建议，虽然localStorage也有同源限制，但postMessage和iframe相结合可以实现跨域消息传递，可能会有泄露用户信息的风险
const handleLogin = (username, password) => {
  return new Promise((resolve, reject) => {
    login(username, password).then(_ => {
      isLogin = true
      window.localStorage.setItem('username', username)
      window.localStorage.setItem('password', password)
      // dosometing
      resolve()
    }).catch(err => {
      // dosometing
      reject(err)
    })
  })
}
// 全局路由守卫。
router.beforeEnter((to, from, next) => {
  if (isLogin) {
    next()
  }
  const username = window.localStorage['username]
  const password = window.localStorage['password]
  if (username && password) {
    handleLogin(username, password).then(_ => {
      next()
    }).catch(err => {
      next('/login')
    })
  } else {
    next('/login)
  }
})
```
#### 3. 客户端保存返回的JWT
代码实现与2相似，主要是后端的登录逻辑。应该将登录逻辑抽象成一个服务，再注入到应用中。可以应对后端登录逻辑的改变。例如
```
const saveLoginInfo = () => {}
const getLoginInfo = () => {}

const login = () => {}
const loginCheck = () => {}
```

#### 4. 全局错误处理
使用axios拦截器
```
axios.interceptors.response.use((response) => {
  return response
}, (error) => {
  // dosomething
  return Promise.reject(error)
})
```

### 2.3 用户信息、磁盘信息等模块
都是使用vue-element-admin后台管理方案。用于信息展示和发送命令远程操作服务器。
#### 1. 多组件(多页面)信息共享
通过后端接口获取数据与展示信息的组件是一对多的关系。即同一信息在多个组件(页面)展示，那么可以在组件创建时发送请求，副作用改变数据模型。这样的好处在于逻辑简单，可以及时获取最新信息，不需要另外维护数据模型。如果引入Vuex则需要维护Vuex中的数据，将数据获取逻辑等都放在Vuex中，对外暴露actions和getters，组件通过提交actions，经过mutations和state，改变getters。
#### 2. 何时获取/更新数据
##### 1. 获取数据
这里的数据是通过外部注入的通过异步操作获取的数据。实际上视图更新是在update时更新的，因此，数据获取在挂载前后的哪一阶段都可以。在Vue中通常在created。还有一点，如果vuex中已有相应的数据，是否还需要重复获取数据?(可以设置时间戳，例如如果上一次数据获取在5分钟前，那么就使用原数据，不重复更新数据，相当于设置缓存时间)。例如
```
let lastTime = 0
let interval = 5 * 60 * 1000
const state = {
  data = null
}
const mutation = {
  UPDATE_DATA(state, data) {
    state.data = data
    lastTime = new Date()
  }
}
const actions = {
  getDate({ commit }, lastTime) {
    return new Promise((resolve, reject) => {
      if (new Date() - lastTime >= interval) {
        // 获取数据
        api.get().then(res => {
          const { data } = res
          commit('UPDATE_DATA', data)  // 更新data
          resolve(data)
        }).catch(err => {
          reject(err)
        })
      } else {
        reject('no get')
      }
    })
  }
}
```
##### 2. 更新数据
更新数据,更新数据的时机，这取决于后端所提供的接口。例如用户模块。添加用户后，需要重新获取用户信息。这里有一个问题，需要设置定时器，定时更新数据吗?避免数据滞后。这取决你的需求。

## 3. 项目思考
### 3.1 问题
1. 根据后端提供的接口，有些接口(例如，关机命令)很危险，很难进行调试。
2. 接口过多，有些post接口会改变多个get接口所获取的信息。文档不完善，需要与后端同时进行多次沟通确定。
3. 这个项目是使用element-ui搬砖项目，锻炼了搬砖熟练度。