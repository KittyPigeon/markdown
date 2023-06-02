# 1.请输出下面下面输出内容，考察内容变量提升；

基础题

```javascript
console.log(a);
var a = 25;
console.log(a);
function foo() {
  console.log(a);
  var a = 30;
  console.log(a);
}
console.log(a);
foo();
```

答案：undefined 25 undefined 30;

扩展题：
代码 1

```javascript
function test(m) {
  m = {
    v: 5,
  };
}
var m = {
  k: 30,
};

test(m);
alert(m.v);
// undefined
```

解析：函数参数引用了 m,对 m 进行了重新赋值(不再引用同一个地址)，内部操作 m 和外部没有关联

代码 2

```javascript
function test(m) {
  m.xxx = {
    v: 5,
  };
}
var m = {
  k: 30,
};

test(m);
console.log(m);
// {k: 30, xxx: {…}}
```

解析：此事 xxx 引用同一个地址

ps:此处考察了变量类型

代码 3

```javascript
function yd() {
  console.log(1);
}

(function () {
  if (false) {
    function yd() {
      console.log(2);
    }
  }

  yd();
})();

// yd is not a function
```

解析：立即函数里 yd 函数声明被提升，但是函数体未被提升出来，所以是 yd 是 undefined

# 2.关于 this 指向问题

## 先看下普通函数的 this

```javascript
function fn() {
  console.log(this);
}
var obj = {
  x: 1,
  fn: fn,
};
// 对象方法调用
obj.fn();
// 直接调用
fn();
// 构造函数调用
new fn();
fn.apply(obj, [1, 2, 3]);
fn.call(obj, 1, 2, 3);
fn.bind(obj, 1, 1, 2)();
```

从上面的代码看出

1. 普通函数里的 this 指向是调用此函数的对象
2. 如果函数用作构造函数，this 指向构造函数创建的对象实例
3. 可以通过 apply,call,bind 改变普通函数 this 指向
4. 当对象方法里的普通函数，被单独调用，它的 this 就指向了 window；可以通过改变为箭头函数、apply 等方法改变

## 箭头函数里的 this

```javascript
var fn = () => {
  console.log(this);
};
var obj = {
  x: 1,
  fn: fn,
};
fn(); // window
obj.fn(); // window
fn.apply(obj); // window
fn.call(obj); // window
fn.bind(obj)(); // window
```

总结：

1. 箭头函数本身没有 this 但是它声明的时候可以捕获别人的 this 供自己用
2. 箭头函数中的 this 是它在声明的时候捕获它所处的 this
3. this 一旦被捕获，就无法改变；即使是 apply,call,bind 也不行
4. 箭头函数无法被实例化

扩展题：
题目 1：

```javascript
function fn() {
  console.log(this.length);
}

var init = {
  length: 5,
  method: function () {
    "use strict";
    fn();
    arguments[0]();
  },
};
const result = init.method.bind(null);
result(fn, 1);
// 0 2
// this指向问题 this软绑硬绑 严格模式
```

# 3.请写出如下点击如下 li 输出，并用 3 中方法正确输出 li 的值 考察知识点：闭包；let 的块级作用域

```javascript
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <li>6</li>
</ul>
```

## 解法 1 使用 this

因为 function 的 this 指向当前调用它的 dom

```javascript
var list_li = document.getElementsByTagName("li");
for (var i = 0; i < list_li.length; i++) {
  list_li[i].onclick = function () {
    console.log(this.innerHTML);
  };
}
```

## 解法 2 使用 let

解答：let 存在块级作用域

```javascript
var list_li = document.getElementsByTagName("li");
for (let i = 0; i < list_li.length; i++) {
  list_li[i].onclick = function () {
    console.log(i);
  };
}
```

## 解法 3 使用闭包

```javascript
var list_li = document.getElementsByTagName("li");
for (let i = 0; i < list_li.length; i++) {
  list_li[i].onclick = (function (x) {
    console.log(x);
  })(i);
}
```

# 请用一句话遍历变量 a。禁止使用 for，已知 a='abc';

```javascript
console.log(...new Set("abc"));
console.log(Array.from("abc"));
console.log(Array.proptype.slice.call("abc"));
// Set可接受具有iterable接口的其它数据结构作为参数
```

# 4. 请问变量 a 会被 GC 回收么，为什么呢？考察点：垃圾回收

```javascript
function test() {
  var a = "init";
  return function () {
    eval("");
  };
}
test()();
// 不会回收
```

总结 ：

1. eval 不对词法作用域任何的变量进行解除绑定
2. with 浏览器一旦遇到 with 放弃所有变量的回收
3. 如果调用了大函数

# 5. 原型链的考察

对象属性查找：对象->构造函数->**proto**->prototype
基础题：
题目 1：

```javascript
function Person() {
  this.a = "a";
}
var p = new Person();
// 考题1：
//   p.__proto__.a='c';
//   console.log(Person.prototype.a) // 因为有同一个引用地址，这个是跟new机制有关
console.log(p.a);
```

题目 2

```javascript
Object.prototype.a = "a";
Function.prototype.a = "a1";
function Person() {}
var init = new Person();
console.log("p.a: " + init.a);
console.log(init.__proto__.__proto__.constructor.constructor.constructor);
// p.a: a
// Function() { [native code] }
```

1. 此处考题 1 **proto**和 Person.prototype 是同一个内存地址
