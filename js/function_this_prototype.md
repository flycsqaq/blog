# Function
想象一下，如果没有Function, 对象的继承可能是这样子的。下面以prototypeOrigin为起点
```
const b = {} // b.__proto__隐式地指向prototypeOrigin
const c = {}
b.__proto__ = c // 修改原型链
```
但这样做需要引入类来引入批量生产对象的能力。
而js则是用构造函数来模拟类。因此需要引入原始的构造函数来生成Function。通过Function生成构造函数有以下规则
1. 生成prototype属性,且prototype.constructor指向该函数。除Object之外,prototype.__proto__指向Object.prototype
2. __proto__指向Function.prototype
3. Function.prototype.__proto__ === Obejct.prototype
而通过构造函数生成对象
```
function a() {}
const b = new a()
b.__proto__ === Function.prototype // true
```
如果要深入Object与Function的话。
Object.prototype 与 Function.prototype是最原始的。而Object是由Function生成的构造函数。因此```Object.__proto__ === Function.prototype```。很巧妙的设计，但有点绕。
因此，通过Object比通过Function生成对象少一层原型链。但Function可以批量生产对象。
## api
1. bind(this, arg1, arg2)
2. call(this, arg1, arg2)
3. apply(this, [arg1, arg2])

## 函数内部关键字
### arguments
参数列表，类数组。```Array.from(arguments)``` 转化为数组。
### this
调用函数的环境，紧接的上一次get操作的结果。