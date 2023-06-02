1. 手写实现一个 new
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

2. 手写实现 instanceof
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
