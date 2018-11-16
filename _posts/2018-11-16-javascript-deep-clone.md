---
layout: post
title: 'Javascript:拷贝对象'
date: 2018-11-016
categories: Javascript
tags: Javascript
author: Victor Parmar | 翻译 by 李昕
cover: 'https://lixin.blog/assets/img/javascript_banner.png'
---
[阅读原文](https://qbview.url.cn/getResourceInfo?appid=62&url=https%3A%2F%2Fsmalldata.tech%2Fblog%2F2018%2F11%2F01%2Fcopying-objects-in-javascript%3Ffrom%3Dtimeline%26isappinstalled%3D0%26nsukey%3DyoMsSBDiV4D8qD1iCPS%252FsN1yYpTsA6wSuMt1ma5RWIe8yufL99lP0DQWEd8buHWQDhY8GeXlWj57K0EB4RtFcC4zOzzma0MuZXOxTYg2vmfjfo6fp7etIzGM1Rze8aWLQEknmZk75s4wXRtcNfHVdw48svUdUKDRzDQoVnGExLlVWlf%252BpadeoSN66t7ceyoBMNmehGLpPQ6HN63yGFl50A%253D%253D&openid=ooa-VuBXWSQuHMAx8XIT_z5F06ng&version=10000&doview=1&platformtype=600)

在这篇文章中，我们将介绍在Javascript中复制对象的各种方法。探究一下浅复制和深复制。

在开始之前，有些基础知识值得一提：Javascript中的对象只是对内存中某个位置的引用。这些引用是可以改变的，即它们可以被重新分配。因此，仅仅复制引用只会导致有两个个引用指向内存中的同一位置：

```javascript
var foo = {
    a : "abc"
}
console.log(foo.a); // abc

var bar = foo;
console.log(bar.a); // abc

foo.a = "yo foo";
console.log(foo.a); // yo foo
console.log(bar.a); // yo foo

bar.a = "whatup bar?";
console.log(foo.a); // whatup bar?
console.log(bar.a); // whatup bar?   
```
在上面的例子中，我们可以看出foo和bar都被修改了。因此在Javascript中复制对象时，根据你的情况，可能需要有些注意的地方。

**浅拷贝**

如果对象只具有值类型的属性，则可以使用扩展语法或者*Object.assign(...)*

```javascript
var obj = { foo: "foo", bar: "bar" };
var copy = { ...obj }; // Object { foo: "foo", bar: "bar" }
```

```javascript
var obj = { foo: "foo", bar: "bar" };
var copy = Object.assign({}, obj); // Object { foo: "foo", bar: "bar" }
```

注意上面两个方法都可以将属性值从多个对象复制到目标对象

```javascript
var obj1 = { foo: "foo" };
var obj2 = { bar: "bar" };

var copySpread = { ...obj1, ...obj2 }; // Object { foo: "foo", bar: "bar" }
var copyAssign = Object.assign({}, obj1, obj2); // Object { foo: "foo", bar: "bar" }
```

上面的方法的问题在于，对于属性就是对象的对象，只能复制属性对象的引用，即等同于var bar = foo;与第一个例子相同：

```javascript
var foo = { a: 0 , b: { c: 0 } };
var copy = { ...foo };

copy.a = 1;
copy.b.c = 2;

console.dir(foo); // { a: 0, b: { c: 2 } }
console.dir(copy); // { a: 1, b: { c: 2 } }
```
*修改了copy.b.c，也导致了foo.b.c发生了更改，因为copy.b.c是foo.b.c的引用*

**深拷贝（有限制）**

为了深拷贝一个对象，可行的方法是将对象序列化为一个字符串，然后将其反序列化：

```javascript
var obj = { a: 0, b: { c: 0 } };
var copy = JSON.parse(JSON.stringify(obj));
```
不过很遗憾这个方法只有在被复制对象包含可序列化的值类型，并且没有任何循环引用时才有效。一个不可序列化的例子比如*Date*对象，即使他可以用标准格式打印出来，使用*JSON.parse*只把他解释成字符串，而不是*Date*对象。

**深拷贝（较少限制）**

对于更加复杂的情况，我们可以使用HTML5新的克隆算法——结构化克隆，遗憾的是，在这篇文章编写之时，这个方法仍然局限于某些内置类型，但是它支持的类型还是要比*JSON.parse*多很多：Date, RegExp, Map, Set, Blob, FileList, ImageData, 稀疏和类型化的Array。它还保留了被克隆对象的引用，允许它支持诸如循环结构和递归结构这些上述序列化方式不支持的结构。

目前没有直接的方法来调用结构化克隆算法，但是有一些较新的浏览器功能使用了这种算法。因此，我们可以找到一些变通方法来实现深拷贝。

*通过MessageChannels:*这个方法是idea是来自通讯功能中的序列化算法，由于这个功能是以事件为基础的，因此克隆也是异步操作。

```javascript
class StructuredCloner {
  constructor() {
    this.pendingClones_ = new Map();
    this.nextKey_ = 0;

    const channel = new MessageChannel();
    this.inPort_ = channel.port1;
    this.outPort_ = channel.port2;

    this.outPort_.onmessage = ({data: {key, value}}) => {
      const resolve = this.pendingClones_.get(key);
      resolve(value);
      this.pendingClones_.delete(key);
    };
    this.outPort_.start();
  }

  cloneAsync(value) {
    return new Promise(resolve => {
      const key = this.nextKey_++;
      this.pendingClones_.set(key, resolve);
      this.inPort_.postMessage({key, value});
    });
  }
}

const structuredCloneAsync = window.structuredCloneAsync =
    StructuredCloner.prototype.cloneAsync.bind(new StructuredCloner);


const main = async () => {
  const original = { date: new Date(), number: Math.random() };
  original.self = original;

  const clone = await structuredCloneAsync(original);

  // different objects:
  console.assert(original !== clone);
  console.assert(original.date !== clone.date);

  // cyclical:
  console.assert(original.self === original);
  console.assert(clone.self === clone);

  // equivalent values:
  console.assert(original.number === clone.number);
  console.assert(Number(original.date) === Number(clone.date));

  console.log("Assertions complete.");
};

main();
```
*通过历史记录API*：*history.pushState()* 和 *history.replaceState()*都会创建他们第一个参数的结构化克隆。注意，虽然这个方法是同步的，但操作浏览器历史记录并不是一项快速操作，并且反复调用此方法可能会导致浏览器无响应。

```javascript
const structuredClone = obj => {
  const oldState = history.state;
  history.replaceState(obj, null);
  const clonedObj = history.state;
  history.replaceState(oldState, null);
  return clonedObj;
};
```
*通过通知API*：在创建新的浏览器通知时，构造函数会根据其关联的数据创建结构化的克隆。注意，他会企图给用户显示一个浏览器通知，但除非应用程序已请求显示通知的权限，否则将以静默方式失败。在授予权限的情况下，通知立即关闭。

```javascript
const structuredClone = obj => {
  const n = new Notification("", {data: obj, silent: true});
  n.onshow = n.close.bind(n);
  return n.data;
};
```

**NODE.JS中的深拷贝**

从8.0.0开始，Node.js提供了与结构化克隆兼容的序列化API。请注意，在撰写本文时，此API已标记为实验性：

```javascript
const v8 = require('v8');
const buf = v8.serialize({a: 'foo', b: new Date()});
const cloned = v8.deserialize(buf);
cloned.b.getMonth();
```

对于低于8.0.0的版本或想要更稳定的实现方式，可以使用lodash的cloneDeep方法，该方法也基于结构化克隆算法。

**结论**

总而言之，在Javascript中复制对象的最佳算法在很大程度上取决于你要复制的对象的上下文和类型。虽然lodash是通用的深拷贝最安全的选择，但如果你自己动手，可能会获得更高效率的实现，以下是一个适用于日期的简单深度克隆的示例：

```javascript
function deepClone(obj) {
  var copy;

  // Handle the 3 simple types, and null or undefined
  if (null == obj || "object" != typeof obj) return obj;

  // Handle Date
  if (obj instanceof Date) {
    copy = new Date();
    copy.setTime(obj.getTime());
    return copy;
  }

  // Handle Array
  if (obj instanceof Array) {
    copy = [];
    for (var i = 0, len = obj.length; i < len; i++) {
        copy[i] = deepClone(obj[i]);
    }
    return copy;
  }

  // Handle Function
  if (obj instanceof Function) {
    copy = function() {
      return obj.apply(this, arguments);
    }
    return copy;
  }

  // Handle Object
  if (obj instanceof Object) {
      copy = {};
      for (var attr in obj) {
          if (obj.hasOwnProperty(attr)) copy[attr] = deepClone(obj[attr]);
      }
      return copy;
  }

  throw new Error("Unable to copy obj as type isn't supported " + obj.constructor.name);
}
```

就个人而言，我期待能够在到处都使用结构化克隆，让这个问题最终不是问题，克隆快乐:)