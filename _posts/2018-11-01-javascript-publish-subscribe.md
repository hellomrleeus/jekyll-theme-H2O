---
layout: post
title: 'Javascript:发布/订阅模式的简单模拟'
date: 2018-11-01
categories: Javascript
tags: Javascript
---

```js
let events = {};
let publish = function (eventName, content) {
    if (!events[eventName])
        events[eventName] = [];
    events[eventName].push(content);
    if (!events['__' + eventName])
        return;
    for (let i = 0; i < events['__' + eventName].length; i++) {
        events['__' + eventName][i](content);
    }
}

let subscribe = function (eventName, callback) {
    if (!events['__' + eventName])
        events['__' + eventName] = [];
    events['__' + eventName].push(callback);
    if (!events[eventName])
        return;
    for (let i = 0; i < events[eventName].length; i++) {
        callback(events[eventName][i]);
    }
}
```