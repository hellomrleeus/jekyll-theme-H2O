---
layout: post
title: "Javascript:ES6中export、export default和import"
date: 2018-11-01
categories: Javascript
tags: Javascript
author: 李昕
cover: "https://lixin.blog/assets/img/javascript_banner.png"
---

export，import ，export default 是什么

ES6 模块主要有两个功能：export 和 import

export 用于对外输出本模块（一个文件可以理解为一个模块）变量的接口

import 用于在一个模块中加载另一个含有 export 接口的模块。

也就是说使用 export 命令定义了模块的对外接口以后，其他 JS 文件就可以通过 import 命令加载这个模块（文件）。

比如在 a.js 中有如下内容：

```js
export let name = "lee";
```

在其它文件里引用如下：

```js
import { name } from "a.js";

console.log(name);
//lee
```

导出多个变量就应该按照下边的方法：

```js
let name1 = "hello";

let name2 = "lee";

export { name1, name2 };
```

在其他文件里引用如下：

```js
import { name1, name2 } from "a.js";

console.log(name1);
//hello
console.log(name2);
//lee
```

导出函数如下：

```js
function add(x, y) {
  alert(x + y);
}

export { add };
```

在其他文件里引用如下：

```js
import { add } from "a.js";

add(4, 6); //10
```

总结

1、export 与 export default 均可用于导出常量、函数、文件、模块等

2、你可以在其它文件或模块中通过 import+(常量 \| 函数 \| 文件 \| 模块)名的方式，将其导入，以便能够对其进行使用

3、在一个文件或模块中，export、import 可以有多个，export default 仅有一个

4、通过 export 方式导出，在导入时要加{ }，export default 则不需要
