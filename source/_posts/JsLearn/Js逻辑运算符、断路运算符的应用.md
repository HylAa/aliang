---
title: Js - 逻辑运算符、断路运算符的应用
tags:
  - Js
categories: []
date: 2022-09-13 09:48:00
updated: 2022-09-13 09:48:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1663033792/JS/js/60e827295bbd94184241a2ed_5d5b7df8da745af0c99a7fe0_5d185a3253838e08756ce517_javascript_resize_iigg5q.png
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1663033792/JS/js/60e827295bbd94184241a2ed_5d5b7df8da745af0c99a7fe0_5d185a3253838e08756ce517_javascript_resize_iigg5q.png
sticky: 1
---



### 1、逻辑运算符

1. &&

   例如 a&&b 

   解释1：只要&&前面是false 都会反回这个值 。

   解释2：只要&&前面是true，无论后面是true还是false都将反回&&后面的值。

2. ||

   例如 a||b

   解释1：只要||前面为false，无论||后面是true还是false，都将反回||后面的值。

   解释2：只要||前面为true，无论||后面是true还是false，都将反回||前面的值。

---

```js
let flag = true;
if(flag){
  console.log(1);
}
//等于
console.log(flag&&1);// 前面的值为true（存在）则返回后面的值 或者执行函数
```



```js
let flag = false;
if(!flag){
  console.log(1);
}
//等于
console.log(flag||1);// 前面的值为false（不存在）则返回后面的值 或者执行函数
```



```js
//进阶用法
let a =5;
let b;
if(a>10){
  b=10;
}else{
  b=5;
}
console.log(b);
//等价于
let a = 5;
let b;
b = a>10&&10||5
```

## 说下逻辑运算符 !!

> 首先在复习下 js 中布尔值为false的六种情况
> 1、undefined（未定义，找不到值时出现）
> 2、null（代表空值）
> 3、false（布尔值的false，字符串"false"布尔值为true）
> 4、0（数字0，字符串"0"布尔值为true）
> 5、NaN（无法计算结果时出现，表示"非数值"；但是typeof NaN===“number”）
> 6、“”（双引号）或’'（单引号） （空字符串，中间有空格时也是true）
> 注意空数组空对象，负值转的布尔值时都为true

##### “!!“操作符是判断变量是否真正为真（非null/undefined/false？0/NaN/””、''等）的很简明的实现方式，省去了大量的&&运算，如果用&&来实现求真逻辑，代码表现如下：

```js
if( obj!==null &&obj!==undefined && obj!==NaN && obj!=="" && obj!==false && obj!==0){
  // 
}
//用!!
if(!!obj){
  //
}
```

## ES11 又新出了一个 ?? 操作符

当左侧是 null 或者 undefined 时候，返回后面的值

```js
null || 1 // 1
undefined || 1 // 
```

有点像 || 运算符

#### 他们两个的区别是

1. 使用 ?? 时，只有One为 null 或者 undefined 时才会返回 two;
2. 使用 || 时，One会先转化为布尔值判断，为true时返回One , false 返回Two

```js
 // ??
  undefined ?? 2    // 2
  null ?? 2        // 2
  0 ?? 2            // 0
  "" ?? 2            // ""
  true ?? 2        // true
  false ?? 2        // false


 // ||
  undefined || 2    // 2
  null || 2        // 2
  0 || 2            // 2
  "" || 2            // 2
  true || 2        // true
  false || 2        // 2

```

后续补充！！！

