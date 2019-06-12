# 程序员笔试题系统
## 1. 简介
谷神星笔试系统，用于对应聘者的笔试筛选，后端Flask，前端Vue。

## 2. 项目模块
### 2.1 登录模块
分为考官与考试两种登录模式。
#### 1. 考生
1. 注册用户,获取登录码
2. 登录
#### 2. 考官
前端路由，输入固定的字符串。跳转至出题/审题界面。
#### 3. 跳转
因此，登录按钮需要对字符串进行判断，选择跳转的界面以及是否发送请求至后端。
```
if (value === 'password1') {
  router.push('/set')
} else {
  api.post(value).then(res => {
    router.push(`./answer/${res.data.code}`)
  })
}
```

### 2.2 答题模块
#### 1. 需求
客户端定时保存数据，点击某个按钮可以将发送post请求将数保存至服务端的数据库。
### 2.3 出题模块
题目以HTML模式保存，在答题界面通过v-html转换。
### 2.4 审题模块
按时间排序，需要对答题者筛选/排序按钮，对数据有分页效果。

demo:
```
const pagesize = 10
const page = 1
const data = []
const orderName = ''
const orderMethod = true // true为正序, false为倒叙
const filter = {} // 空白为不筛选
computed: {
  filterKeys() {
    return Object.keys(this.filter)
  },
  filterData() {
    const keys = this.filterKeys
    const filter = this.filter
    return this.data.filter((d) => {
      return keys.every((key) => {
        return d[key] = filter[key]
      })
    })
  },
  orderSortData() {
    const orderMethod = this.orderMethod
    const orderName = this.orderName
    const orderArr = this.filterData.sort((l,r) => {
      return l[orderName] - r[orderName]
    })
  },
  // 帅选、排序后的数据
  orderData() {
    return this.orderMethod? this.orderSortData: this.orderSortData.reverse()
  },
  start() {
    return this.pagesize * (this.page - 1)
  },
  end() {
    return this.start + this.pagesize
  },
  showData() {
    return this.orderData.slice(start, end)
  }
}
watch: {
  // 定义改变order/filter等之后的操作
}
```
