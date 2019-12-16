---
title: immutable.js
teaser:	持久化数据结构？？
category: study
tags: [React，immutable.js]

---

## 什么是Immutable Data？

扔一段官方文档，我也看不明白（过了个假六级）

>Immutable data encourages pure functions (data-in, data-out) and lends itself to much simpler application development and enabling techniques from functional programming such as lazy evaluation.

实现原理：**Persistent Data Structure （持久化数据结构）**

也就是用旧数据创建新数据的时候要保证旧数据同时可用且不变，而且为了避免深拷贝把所有节点复制一遍带来的性能损耗，Immutable使用了`Structural Sharing`（结构共享），就是说如果对象数中一个节点发生变化，只修改这个节点和受它影响的父节点，其他节点共享，如图

[结构共享]: https://upload-images.jianshu.io/upload_images/2165169-cebb05bca02f1772?imageMogr2/auto-orient/strip|imageView2/2/w/613/format/webp	"结构共享"

## 优点

### 1.降低Mutable带来的复杂度

引用赋值虽然可以节省内存，但是当工程变的庞大，可变状态会成为噩梦，通常是理由深拷贝来避免，但是过度消耗内存

### 2.节省内存空间

因为是结构共享，所以Immutable使用这种方式会尽量复用内存

```javascript
import { Map } from 'immutable';
let a = Map({
  select: 'users',
  filter: Map({ name: 'Cam' })
})
let b = a.set('select', 'people');

a === b; // false
a.get('filter') === b.get('filter'); // true
```

这里只有`select`变化了，所以`filter`节点是共享的

### Undo/Redo , Copy/Paste

每次数据都不一样，所以可以把数据放到数组里存储，想拿哪次得到的数据都可以（话虽如此，我却没用过）

### 函数式编程

---

## 缺点

不说了自己看吧

## Immutable.js的数据类型

>- List：有序序列集，类似Array
>- Map：无序序列集，类似Object
>- Seq：这表示一系列值，但不能由具体的数据结构支持。https://cloud.tencent.com/developer/section/1489381
>- 等……（以后再添加）

## Immutable.js 常用API






















