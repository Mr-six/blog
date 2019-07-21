---
title: js闭包
categories: ['js']
date: 2016-05-08 11:04:38
tags: ['js']
---
感觉闭包是Javascript的一个重点，应该也是面试中常常会考察的一个要点，所以记录下来以便日后查看。
JavaScript的闭包有两个用途：一个是访问函数内部的变量；另一个是让变量的值在作用域内保持不变。函数是JavaScript 中唯一有作用域的对象，因此JavaScript的闭包依赖于函数实现。

### 访问函数内部的变量
JavaScript的函数可以直接访问全局中的变量，但是无法直接访问到函数中的变量。如：
```
var count = 0

function Counter() {
  var count = 2;
  return {
    increment: function() {
      count++;
    },
    get: function() {
      return count;
    }
  }
}
console.log(count)  // -> 0  # 输出全局中的值
var foo = new Counter();
foo.increment();
foo.get();    // -> 3   # 输出函数内的值
console.log(count)  // -> 0  # 输出全局中的值,不受外部影响
```
在上面示例中，count相当于一个“私有变量”，在Counter函数外部不能访问这个变量。在Counter中，还定义了increment 和 get两个“闭包函数”，这两个函数都保持着对Counter作用域的引用，因此可以访问到Counter作用域内定义的变量count。而过度的使用闭包会导致JavaScript的垃圾回收机制不能更好的工作。

### 保持变量的作用域
网上常见到的一段代码：
```
for(var i = 0; i < 10; i++) {
  setTimeout(function() {
    console.log(i);  
  }, 1000);
}
```
毫无疑问，每次都会输出10，因为i为全局中的变量，每次输出都是输出了全局的i

解决方案：使用闭包保存变量
```
for(var i = 0; i < 10; i++) {
  (function(e) {
    setTimeout(function() {
      console.log(e);  
    }, 1000);
  })(i);
}

```
