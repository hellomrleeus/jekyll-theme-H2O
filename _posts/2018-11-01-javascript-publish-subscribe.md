---
layout: post
title: 'Javascript:观察者模式与发布/订阅模式'
date: 2018-11-01
categories: Javascript
tags: Javascript
author: 李昕
cover: 'https://lixin.blog/assets/img/javascript_banner.jpg'
---

简单说观察者模式中目标和观察者是有直接联系的，观察者需要在目标那里注册方法。当目标更新信息就调用观察者注册的方法，传递消息。

```js
function ObserverList(){
  this.observerList = [];
}
ObserverList.prototype.add = function( obj ){
  return this.observerList.push( obj );
};
ObserverList.prototype.count = function(){
  return this.observerList.length;
};
ObserverList.prototype.get = function( index ){
  if( index > -1 && index < this.observerList.length ){
    return this.observerList[ index ];
  }
};
ObserverList.prototype.indexOf = function( obj, startIndex ){
  var i = startIndex;
  while( i < this.observerList.length ){
    if( this.observerList[i] === obj ){
      return i;
    }
    i++;
  }
  return -1;
};
ObserverList.prototype.removeAt = function( index ){
  this.observerList.splice( index, 1 );
};

//目标
function Subject(){
  this.observers = new ObserverList();
}
Subject.prototype.addObserver = function( observer ){
  this.observers.add( observer );
};
Subject.prototype.removeObserver = function( observer ){
  this.observers.removeAt( this.observers.indexOf( observer, 0 ) );
};
Subject.prototype.notify = function( context ){
  var observerCount = this.observers.count();
  for(var i=0; i < observerCount; i++){
    this.observers.get(i).update( context );
  }
};

//观察者
function Observer(){
  this.update = function(){
    // ...
  };
}
```
发布订阅模式中发布者和订阅者没有直接联系，发布者将消息发送到消息池，消息池发送消息到订阅者

```js
var pubsub = {};
(function(myObject) {
    // Storage for topics that can be broadcast
    // or listened to
    var topics = {};
    // An topic identifier
    var subUid = -1;
    // Publish or broadcast events of interest
    // with a specific topic name and arguments
    // such as the data to pass along
    myObject.publish = function( topic, args ) {
        if ( !topics[topic] ) {
            return false;
        }
        var subscribers = topics[topic],
            len = subscribers ? subscribers.length : 0;
        while (len--) {
            subscribers[len].func( topic, args );
        }
        return this;
    };
    // Subscribe to events of interest
    // with a specific topic name and a
    // callback function, to be executed
    // when the topic/event is observed
    myObject.subscribe = function( topic, func ) {
        if (!topics[topic]) {
            topics[topic] = [];
        }
        var token = ( ++subUid ).toString();
        topics[topic].push({
            token: token,
            func: func
        });
        return token;
    };
    // Unsubscribe from a specific
    // topic, based on a tokenized reference
    // to the subscription
    myObject.unsubscribe = function( token ) {
        for ( var m in topics ) {
            if ( topics[m] ) {
                for ( var i = 0, j = topics[m].length; i < j; i++ ) {
                    if ( topics[m][i].token === token ) {
                        topics[m].splice( i, 1 );
                        return token;
                    }
                }
            }
        }
        return this;
    };
}( pubsub ));
```
