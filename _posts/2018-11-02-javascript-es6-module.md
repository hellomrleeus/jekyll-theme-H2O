---
layout: post
title: 'Javascript:ES6中export、export default和import'
date: 2018-11-01
categories: Javascript
tags: Javascript
author: 李昕
---

export，import ，export default是什么

ES6模块主要有两个功能：export和import

export用于对外输出本模块（一个文件可以理解为一个模块）变量的接口

import用于在一个模块中加载另一个含有export接口的模块。

也就是说使用export命令定义了模块的对外接口以后，其他JS文件就可以通过import命令加载这个模块（文件）。

比如在a.js中有如下内容：

```js
export let name = "lee";
```

在其它文件里引用如下：

```js
import { name } from "a.js" 

console.log(name);
//lee
```

导出多个变量就应该按照下边的方法：

```js
let name1="hello";

let name2="lee";

export { name1 ,name2 }
```

在其他文件里引用如下：

```js
import { name1 , name2 } from "a.js"

console.log(name1);
//hello
console.log(name2);
//lee
 ```
 
导出函数如下：

```js
function add(x,y){
   alert(x + y);
}

export { add }
```

在其他文件里引用如下：

```js
import { add } from "a.js"

add(4,6) //10
```

总结

1、export与export default均可用于导出常量、函数、文件、模块等

2、你可以在其它文件或模块中通过import+(常量 | 函数 | 文件 | 模块)名的方式，将其导入，以便能够对其进行使用

3、在一个文件或模块中，export、import可以有多个，export default仅有一个

4、通过export方式导出，在导入时要加{ }，export default则不需要