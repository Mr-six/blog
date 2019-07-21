---
title: 'js 笔记'
date: 2017-01-25 15:52:43
categories: ['js']
tags: ['js']
---
- 基本数据类型
    数值（number）：整数和小数（比如1和3.14）
    字符串（string）：字符组成的文本（比如”Hello World”）
    布尔值（boolean）：true（真）和false（假）两个特定值
    undefined：表示“未定义”或不存在，即此处目前没有任何值
    null：表示空缺，即此处应该有一个值，但目前为空
    对象（object）：各种值组成的集合
- typeof 运算符
    数值、字符串、布尔值分别返回number、string、boolean
    undefined返回undefined
    函数返回function
    除此以外，其他情况都返回object
- 类型判断使用 instanceof
    
- 类型转换
    Number(null)  // 0
    而 null == 0 // false
    Number(undefined) // NaN
    而 null == undefined // true
    Number([])  // 0
    Number({})  // NaN
- Boolean()
  * undefined
  * null
  * -0
  * 0或+0
  * NaN
  * ''（空字符串）
- javascript 第二行不自动添加分号情况（五种）
( ,  [  , /  ,  +  ,  -

- Math 对象方法
  * Math.abs()：绝对值
  * Math.ceil()：向上取整
  * Math.floor()：向下取整
  * Math.max()：最大值
  * Math.min()：最小值
  * Math.pow()：指数运算
  * Math.sqrt()：平方根
  * Math.log()：自然对数
  * Math.exp()：e的指数
  * Math.round()：四舍五入
  * Math.random()：随机数


- String 常用方法
slice(start, stop) （不包括stop位）提取字符串
'JavaScript'.slice(0, 4) // "Java"

以下多用于正则配合
match()
search
replace()
split()  // 可根据参数将字符串分割为数组

- Array 常用方法

Array.isArray() 判断是否为数组

push() 压栈 
  var a = [1, 2, 3];
  var b = [4, 5, 6];
  a.push.apply(a, b)  同 a.concat(b)

pop() 出栈

join('分隔符') 转化为字符串

concat() 连接数组 与push不同的是，他对于数组参数，会将其拆成单一的元素

shift() 删除头部

unshift() 添加到头

reverse() 颠倒排序

slice() 同string的方法 提取数组指定部分

splice(a,b,c……)  a:删除的起始位置 b:被删除的元素个数 c:一个或多个参数 为添加进的数组的元素添加方法同push 不会拆分数组
 
sort() 排序 默认为字典排序 可接收自定义排序函数

map() 遍历元素执行某个函数，并返回新数组 方法所接收的三个参数（element,index,array）

forEach() 同map 但不返回数组

filter() 返回为true 的 元素组成的数组

some() 和 every() 判断数组元素是否满足某个条件

reduce() 和reduceRight() 处理数组成员，最终累计为一个值

indexOf() 和lastIndexOf() 返回给定元素在数组中第一次出现的位置

- Date 对象

转化为ISO时间：
d.toJSON()
// "2012-12-31T16:00:00.000Z"
d.toISOString()
// "2012-12-31T16:00:00.000Z"

转化为UTC时间：
Date.prototype.toUTCString()
var d = new Date(2013, 0, 1);
d.toUTCString()
// "Mon, 31 Dec 2012 16:00:00 GMT"
d.toString()  默认情况下，Date对象返回的都是当前时区的时间
// "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"

返回UTC时间：
Date.UTC()  返回当前距离1970年1月1日 00:00:00 UTC的毫秒数

get方法：
  * getTime()：返回距离1970年1月1日00:00:00的毫秒数，等同于valueOf方法。
  * getDate()：返回实例对象对应每个月的几号（从1开始）。
  * getDay()：返回星期几，星期日为0，星期一为1，以此类推。
  * getYear()：返回距离1900的年数。
  * getFullYear()：返回四位的年份。
  * getMonth()：返回月份（0表示1月，11表示12月）。
  * getHours()：返回小时（0-23）。
  * getMilliseconds()：返回毫秒（0-999）。
  * getMinutes()：返回分钟（0-59）。
  * getSeconds()：返回秒（0-59）。
  * getTimezoneOffset()：返回当前时间与UTC的时区差异，以分钟表示，返回结果考虑到了夏令时因素。

- 正则对象
属性：1）lastIndex 此次匹配成功时的位置  2）source 正则表达式的字符串形式

1. test()  匹配到则返回true

2. exec() 匹配成功返回匹配成功的数组 反正返回null

3. 需要使用 \ 转义的字符
^  .  [  $  (  )  |  *  +  ?  {  \  共12个 

4. 字符串对象方法
1）

- 面向对象
 与枚举性相关的几个操作的区别的是，for...in循环包括继承自原型对象的属性，Object.keys方法只返回对象本身的属性。如果需要获取对象自身的所有属性，不管是否可枚举，可以使用Object.getOwnPropertyNames方法
