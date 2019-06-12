# MVVM模型
MVVM指的是数据模型与视图模型之间的联系。那么这里就会涉及到数据模型与视图模型的设计，以及两者如何联系。

## 1. 需求
### 1.1 数据模型中数据的改变会引起相应的依赖发生变化
在数据模型数据改变发出更新指令，视图模型接收到更新指令后，开始update/render。
即一个数据对应多个视图模型。
```
class dataModel {
  constructor(val) {
    var value = val
    this.deps = []
  }
  update() {
    this.deps.forEach(dep => dep.update())  // 依赖更新
  }
  getValue() {
    return value
  }
  changeValue(val) {
    value = val
    update()
  }
}
// 性能方面可能会有问题
class middleware {
  constroctor() {
    this.content = () => `${data}` // 模板字符串获取内容
    this.value = this.content()
    this.tag = ''
  }
  update() {
    this.value = this.content()
    render()
  }
  render() {
    return dom
  }
}
```
### 1.2 视图模型
视图模型应该就是DOM节点的东西。
1. tag
2. content
3. children
4. event
5. id
6. class
7. property
8. style

我认为使用property进行父子组件传值确实是一件很正常不过的事情，但也会对HTML中的attribute产生一些困惑。视图模型是通过绑定property进行工作，而不是attribute。关于property与attribute的对比可以参考Angular文档中的描述。[HTML attribute 与 DOM property 的对比](https://www.angular.cn/guide/template-syntax#html-attribute-vs-dom-property)

### 1.3 生命周期与副作用
生命周期所对应的是从定义数据模型与视图模型到DOM节点挂载、更新、销毁的过程。因此，需要的生命周期应该为
1. 定义模型 -> 节点挂载：定义模型到节点挂载的中间过程，在这里可以做一些模型的改变，用于继承基类扩展，同时一些无DOM无关的副作用函数也可以在这里使用(ajax请求等)。
2. 节点挂载之后：用于获取DOM节点，做一些控制DOM节点的工作，例如使用laxxx动画库等。
3. 更新：我不知道什么操作应该放在这个过程，因为在我的理解里，数据改变导致模型改变应该是一个自然的过程，而更新这一周期可能是用于避免过多的渲染而存在的。
4. 销毁：用于移除副作用。这里应该是需要框架着重注意的地方。

## 2. 一个简单的MVVM模型
待补充。

