# 数组/对象与映射
## 名词解释
1. 映射：指两个元素的集之间元素相互对应的关系。如果用映射来定义函数，指一对一或多对一映射。
2. 数组：指有序的元素序列，也可以认为是储存多个相同类型数据的集合。
3. 对象：指具有类类型的变量。在js中通常指存储key/value的数据结构。
因此，数组与对象可以说是简单数据在映射后的具体内容。

## 数组(Array)
### 1. 数据类型判断
数组的主要功能是存储多个同类型数据，但对使用者来说，可能会使用数组存储不同类型的数组，再根据其类型做某些判断。即
```
const arr = [1, '1', {}, null, undefiend, true, []]
const iterator = arr.entries()
for (let e of iterator) {
  console.log(typeof e[1])
}
```
### 2. CURD
此外，对每一种数据来说，都需要有增删查改的能力，这一点在数组中尤为复杂。因为数组是某些数据的有序集合，因此其需要有插入/删除并使数组重新排序的功能。即```slice(start, deleteCounter, add1, add2......)```。但此方法在对数组末尾的数据进行操作时会有性能问题，即将“指针从起点逐步移到数组末端”这一步骤可以简化为“在数组末端创建指针”，再进行操作。由此产生了```push/pop```。而在数组开头操作的方法为```unshift/shift```。
### 3. 映射
将数组中的每一项都作为参数传入某个函数，再返回相应的值。这种操作都可以认为是映射。数组中的大部分api都是内置了映射函数与映射的次数。
1. map(function) // 最简单的映射，由自己定义函数
2. filter/some/all/find/findIndex // function映射后，再根据返回的值(Boolean)进行布尔映射
3. reduce/reduceRight((left ,right) => //return something) // 将每次返回的值作为参数代入函数
4. flat // function映射后，再根据返回的值作类型判断
5. join // reduce的一种特殊方法
### 4. 其他
1. sort/reverse // 排序
2. keys/values/entries // 遍历，也可以认为是映射的一种特殊方法
3. slice // 切片
### 5. 定义一个简单的Array类型
暂不考虑优化。
```
class Array() {
  constructor() {
    // arguments是一个对象，类数组对象，因为其在数组类型结构诞生之前就已经存在了
    const arr = new Object()
    let length = 0
    for (var i = 0; i < arguments.length; i++) {
      arr[i] = arguments
      length++
    }
    this.arr = arr
    this.length = length
  }
  push(value) {
    this.arr[this.length] = value
    return this
  }
  slice(start = 0, end = this.length) {
    const arr = new Array()
    for (var i = start; i < end; i++) {
      arr.push(this.arr[i])
    }
    return arr
  }
  map(function) {
    const arr = new Array()
    for (var i = 0; i < this.length; i++) {
      arr[i] = function(this.arr[i])
    }
    return arr
  }
};
```
## 对象(Object)
对Object来说，已经实现了相应的映射，最重要的便是能否对这些结果进行CURD。(具体api见MDN)
