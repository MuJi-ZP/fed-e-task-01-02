### 1. 描述引用计数的工作原理和优缺点
答：  
&nbsp;核心思想：设置引用数，判断引用数是否为0  
&nbsp;基本原理：设置引用计数器，引用关系改变时，修改引用数字，当数字为0时立刻回收  
&nbsp;优点：  
&nbsp;&nbsp;1. 发现垃圾立即回收  
&nbsp;&nbsp;2. 最大限度减少程序暂停  
&nbsp;缺点：  
&nbsp;&nbsp;1. 无法回收循环引用对象  
&nbsp;&nbsp;2. 资源开销大

--
### 2. 描述标记整理算法的工作流程
答：  
&nbsp;&nbsp;第一阶段：遍历所有对象，标记活动对象  
&nbsp;&nbsp;第二阶段：整理空间，移动对象位置，使位置变得连续  
&nbsp;&nbsp;第三阶段：清除无标记对象，回收相应空间  

--
### 3. 描述 V8 中新生代存储区垃圾回收的流程
答：  
&nbsp;&nbsp;新生代储存区采用采用复制算法+标记整理来回收垃圾。  
&nbsp;&nbsp;第一阶段：活动对象储存在From中，  
&nbsp;&nbsp;第二阶段：标记整理后，将活动对象拷贝至To，  
&nbsp;&nbsp;第三阶段：最后将From与To交换，完成释放

--
### 4. 描述增量标记算法在何时使用，及工作原理
答：  
使用时机：在老生代存储区域需要优化内存回收效率时使用  
原理：在触发垃圾回收及之后，直接执行遍历标记操作，将大段的整块的垃圾回收变成小块暂停时间不明显的标记，最后统一回收。
  
--

# 代码题1
### 基于以下代码完成下面的 4 个练习
  
``` javascript
const fp = require('lodash/fp)

// 数据
// horsepower 马力， dollar_value 价格，in_stock 库存
const cars = [
    {name: "Ferrari FF", horsepower: 660, dollar_value: 700000, in_stock: true},
    {name: "Spyker C12 Zagato", horsepower: 650, dollar_value: 648000, in_stock: false},
    {name: "Jaguar XKR-S", horsepower: 550, dollar_value: 132000, in_stock: false},
    {name: "Audi R8", horsepower: 525, dollar_value: 114200, in_stock: false},
    {name: "Aston Martin One-77", horsepower: 750, dollar_value: 1850000, in_stock: true},
    {name: "Pagani Huayra", horsepower: 700, dollar_value: 1300000, in_stock: false}
  ]
``` 

### 练习1:
使用函数组合 fp.flowRight() 重新实现下面这个函数

```javascript
let isLastInStock = function(cars) {
  // 获取最后一条数据
  let last_car = fp.last(cars)
  // 获取最后一条数据的 in_stock 属性值
  return fp.prop('in_stock', last_car)
}
```
答：  

```javscript
const isLastInStock =  fp.flowRight(fp.prop("in_stock"),fp.last)(cars);
//console.log(isLastInStock); false
```
--

### 练习2:
使用函数组合 fp.flowRight()、fp.prop() 和 fp.first() 获取第一个 car 的 name  

答：

``` javascript
const isLastInStock =  fp.flowRight(fp.prop("name"),fp.first)(cars);
// console.log(isLastInStock); Ferrari FF
```
--

### 练习3:
使用帮助函数 _average 重构 averageDollarValue，使用函数组合的方式实现

``` javascript
let _average = function(xs) {
  return fp.reduce(fp.add, 0, xs) / xs.length
}// <-无须改动

let averageDollarValue = function(cars) {
  let dollar_values = fp.map(function(car){
    return car.dollar_value
  }, cars)
  return _average(dollar_values)
}
```
答：
  
```
let averageDollarValue = fp.flowRight(_average,fp.map(car=>{return car.dollar_value}));
//console.log(averageDollarValue(cars));790700
```

### 练习4:
使用 flowRight 写一个 sanitizeNames() 函数，返回一个下划线连接的小写字符串，把数组中的 name 转换为这种形式：例如：sanitizeNames(["Hello World"]) => ["hello_world"];

答：

```javascript
let sanitizeNames = fp.flowRight(fp.map(carName =>
    fp.toLower(carName)
), fp.map(cars => {
    return fp.replace(/\s+|\s+/g, "_", cars.name);
}));
```

--
# 代码题2
### 基于以下代码完成下面的 4 个练习

```javascript
// support.js
class Container {
  static of (value) {
    return new Container(value)
  }
  constructor(value) {
    this._value = value
  }
  map(fn) {
    return Container.of(fn(this._value))
  }
}
class Maybe {
  static of (x) {
    return new Maybe(x)
  }
  isNothing(){
    return this._value === null || this._value === undefined 
  }
  constructor(x){
    this._value = x
  }
  map(fn) {
    return this.isNothing() ? this : Maybe.of(fn(this._value))
  }
}

module.exports = {
  Maybe,
  Container
}
```

### 练习1:
使用 fp.add(x,y) 和 fp.map(f,x) 创建一个能让 functor 里的值增加的函数 ex1

```javascript
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./supports)

let maybe = Maybe.of([5,6,1])

let ex1 = // 需要实现的位置
```
答：  

```javascript
let maybe = Maybe.of([5,6,1])
let ex1 = addNum => maybe.map(fp.map(maybeArry => fp.add(maybeArry, addNum)));
```
--

### 练习2:
实现一个函数 ex2，能够使用 fp.first 获取列表的第一个元素  

```javascript
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./supports)
let xs = Container.of(['do', 'ray', 'me', 'fa', 'so', 'la', 'ti', 'do'])
let ex2 = //...你需要实现的位置
```
答：

```javacsript
let xs = Container.of(['do', 'ray', 'me', 'fa', 'so', 'la', 'ti', 'do'])
let ex2 = xs => xs.map(fp.first)._value;
```
--

### 练习3:
实现一个函数 ex3，能够使用 safeProp 和 fp.first 找到 user 的名字的首字母

```javascript
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./supports)

let safeProp = fp.curry(function(x,o){
  return Maybe.of(o[x])
})
let user = {id: 2, name: 'Albert'}
let ex3 = //...你需要实现的位置
```
答：  

```javascript
let user = {id: 2,name: 'Albert'}
let ex3 = (name, obj) =>fp.first(safeProp(name)(obj)._value);
//console.log(ex3("name", user));
```
--
### 练习4:
实现 Maybe 重写 ex4，不要有 if 语句  

```javascript
const fp = require('lodash/fp)
const {Maybe, Contaiber} = require('./supports)

let ex4 = function (n) {
  if (n) {return parseInt(n)}
}
```
答：

```javascript
let ex4 = n=> Maybe.of(n).map(parseInt)._value;
console.log(ex4(2))
```
