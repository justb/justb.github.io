---
layout: post
title:  "setTimeout&Promise"
date:   2017-6-24 23:45:13 -0400
background: '/img/posts/01.jpg'
---



## 浏览器渲染过程

```
setTimeout(function(){console.log(4)},0);
new Promise(function(resolve){
    console.log(1)
    for( var i=0 ; i<10000 ; i++ ){
        i==9999 && resolve()
    }
    console.log(2)
}).then(function(){
    console.log(5)
});
console.log(3);
```

首先整个script代码和setTimeout是存放在macrotask queue（宏任务）中的，promise.then是在microtask queue中（微任务）的，
取1个macrotask queue中的task（整个代码），执行之，将promise.then和setTimeout挂起，存放到对应的队列中，
然后把所有microtask queue中的所有任务（promise.then）执行完，再取macrotask queue中的一个任务（setTimeout）执行。

一个浏览器环境（unit of related similar-origin browsing contexts.）只能有一个事件循环（Event loop），
而一个事件循环可以多个任务队列（Task queue），每个任务都有一个任务源（Task source），
相同任务源的任务，只能放到一个任务队列中。不同任务源的任务，可以放到不同任务队列中。

优先级 process.nextTick > promise.then，setTimeout > setImmediate





