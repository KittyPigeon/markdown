# 1. 手写实现一个 new

手写 new 主要考察原型链中**proto**指向，以及 apply 函数的使用

```javascript
function create(fn, ...args) {
  var target = {}; // 1.创建上下文
  // 此处相当于 target.__proto__ = fn.prototype;
  Object.setPrototypeOf(target, fn.prototype); // 2.改变原型指向
  var result = fn.apply(target, args); // 3.改变构造函数this指向
  return result instanceof Object ? target : result; // 4.处理构造函数返回
}
```

# 2. 手写实现 instanceof

考试对象的原型；原型链

```javascript
// 手写instanceof 依照原型链逐级向上查找
function myInstanceof(obj, constructorFun) {
  if (obj == null || obj == undefined) return false;
  let proto = Object.getPrototypeOf(obj);
  while (proto != null) {
    if (proto == constructorFun.prototype) return true;
    proto = Object.getPrototypeOf(proto);
  }
  return false;
}
function Person() {}
var p = new Person();
console.log(myInstanceof(p, Person));
```

# 3. 数组扁平化

将多维数组变成一维数组
方法一 数组 flat 方法

```javascript
var arr = [1, [2], [3, 4, 5, [5, [6]]]];
console.log(arr.flat());
```

讲解：flat() 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。

使用 Infinity 作为深度，展开任意深度的嵌套数组

本质上就是是归纳（reduce） 与 合并（concat）的操作

方法二 JSON.stringify

```javascript
var arr = [1, [2, [3], 4, 5]];
var result = JSON.parse("[" + JSON.stringify(arr).replace(/\[|\]/gi, "") + "]");
console.log("result: " + result);
```

方法三 reduce 递归实现

```javascript
var arr = [1, [2, [3], 4, 5]];
function flatArr(arr) {
  return arr.reduce(function (pre, cur) {
    return pre.concat(Array.isArray(cur) ? flatArr(cur) : cur);
  }, []);
}
```

方法四 函数递归

# 4.数组去重

- new Set 方式
- indexOf 方式
- include 方式
- map 方式

# 类数组转数组

- Array.prototype.slice.call(arguments)
- 结构运算符
- Array.from(arguments)
  代码如下

```javascript
function tool(...args) {
  console.log(Array.prototype.slice.call(arguments));
  console.log(args);
  // 从一个新的类似数组、可迭代对象创造成一个新数组
  console.log(Array.from(arguments));
}
```
