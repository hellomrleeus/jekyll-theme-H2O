---
layout: post
title: 'Javascript:call apply bind'
date: 2018-11-01
categories: Javascript
tags: Javascript
---

call：调用一个对象的一个方法，用另一个对象替换当前对象。例如：B.call(A, args1,args2);即A对象调用B对象的方法。

```js
function Man(){
    this.food = 'fish';
    this.eat = function(){
        alert(this.food);
    }
}
function Cat(){
    this.food = 'null';
}
var cat = new Cat();
Man.call(cat);
cat.eat()
//fish
```
apply：调用一个对象的一个方法，用另一个对象替换当前对象。例如：B.apply(A, arguments);即A对象应用B对象的方法。

```js

function Person(name,age){
    this.name = name;
    this.age = age;
}

function Student(name,age,grade){
    Person.apply(this, arguments);
    this.grade=grade;
}

var student=new Student("zhangsan",21,"一年级");

alert("name:"+student.name+"\n"+"age:"+student.age+"\n"+"grade:"+student.grade);
//name:zhangsan age:21  grade:一年级
```

bind: 不会立即执行，而是返回一个函数，函数中this包含调用者的属性

```js
function Eat(firstName, lastName){
    this.food = 'fish';
    alert(lastName + firstName + "_eat_" + this.food);
}
function Cat(){
    this.firstName = 'cat'
    this.lastName = 'Mr.';
}
var cat = new Cat(); 
var action = Eat.bind(cat, cat.firstName, cat.lastName);
action();
//Mr.cat_eat_fish
```
比如在jq的ajax回调函数中使用this

```js
function AjaxGet(){
    this.name = '123';
    $.get('get.php', function(data){
        console.log(this.name)  //123
    }.bind(this));
}
```