---
title: immutable.js
teaser:	持久化数据结构？？
category: study
tags: [React,immutable.js]

---

<img src="https://upload-images.jianshu.io/upload_images/2165169-cebb05bca02f1772?imageMogr2/auto-orient/strip|imageView2/2/w/613/format/webp" style="zoom:80%;" />

## 什么是Immutable Data？

扔一段官方文档，我也看不明白（过了个假六级）

>Immutable data encourages pure functions (data-in, data-out) and lends itself to much simpler application development and enabling techniques from functional programming such as lazy evaluation.

实现原理：**Persistent Data Structure （持久化数据结构）**

也就是用旧数据创建新数据的时候要保证旧数据同时可用且不变，而且为了避免深拷贝把所有节点复制一遍带来的性能损耗，Immutable使用了`Structural Sharing`（结构共享），就是说如果对象数中一个节点发生变化，只修改这个节点和受它影响的父节点，其他节点共享，如图



## 优点

### 1.降低Mutable带来的复杂度

引用赋值虽然可以节省内存，但是当工程变的庞大，可变状态会成为噩梦，通常是理由深拷贝来避免，但是过度消耗内存

### 2.节省内存空间

因为是结构共享，所以Immutable使用这种方式会尽量复用内存

```dart
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
>
>- Map：无序序列集，类似Object
>
>- Seq：这表示一系列值，但不能由具体的数据结构支持。
>
> [参考]: https://cloud.tencent.com/developer/section/1489381
>
>  
>
>- 等……（以后再添加）

## Immutable.js 常用API

- `fromJS()`

  - 作用：把一个JS数据转换成Immutable类型数据
  - 用法：`fromJS(value , converter)`
  - 参数：value是要转变的数据，converter[可选]是要做的操作，默认会把数组转成LIst，对象转成Map

- `toJS()`

  - 作用：把Immutable类型数据转换成JS类型数据
  - 用法：`value.toJS()`

- `is()`

  - 作用：对两个对象进行比较
  - 用法：`is(map1 , map2)`
  - 简介：和JS中的对象比较不同，JS中比较两个对象是比较地址，而Immutable中比较的是对象的hashCode和valueOf，只要两者hashCode相等，值就相同，避免深度遍历，提高性能

- `List()`和`Map()`

  - 作用：创建一个新的List/Map对象

  - 用法

  - ```dart
    //List
    Immutable.List(); // 空List
    Immutable.List([1, 2]);
    
    //Map
    Immutable.Map(); // 空Map
    Immutable.Map({ a: '1', b: '2' });
    ```

- `get()`、`getIn()`

  - 获取数据结构中的数据

  - ```dart
    //获取List索引的元素
    ImmutableData.get(0);
    
    // 获取Map对应key的value
    ImmutableData.get('a');
    
    // 获取嵌套数组中的数据
    ImmutableData.getIn([1, 2]);
    
    // 获取嵌套map的数据
    ImmutableData.getIn(['a', 'b']);
    ```

- `has()`、`hasIn()`

  - 判断是否存在某个key

  - ```dart
    Immutable.fromJS([1,2,3,{a:4,b:5}]).has('0'); //true
    Immutable.fromJS([1,2,3,{a:4,b:5}]).has('0'); //true
    Immutable.fromJS([1,2,3,{a:4,b:5}]).hasIn([3,'b']) //true
    ```

- 设置`set()`、`setIn()`

  - set  =>  设置第一层key ， index的值

  - setIn =>  设置深层结构中属性的值

  - ```dart
    const originalList = List([ 0 ]);
    // List [ 0 ]
    originalList.set(1, 1);
    // List [ 0, 1 ]
    originalList.set(0, 'overwritten');
    // List [ "overwritten" ]
    originalList.set(2, 2);
    // List [ 0, undefined, 2 ]
    
    List().set(50000, 'value').size;
    // 50001
    
    const originalMap = Map()
    const newerMap = originalMap.set('key', 'value')
    const newestMap = newerMap.set('key', 'newer value')
    
    originalMap
    // Map {}
    newerMap
    // Map { "key": "value" }
    newestMap
    // Map { "key": "newer value" }
    ```

  - **跟js中不同，List中不存在空位，[,,,],List中若没有值，则为undefined。**

  - `setIn()`

  - ```dart
    const originalMap = Map({
      subObject: Map({
        subKey: 'subvalue',
        subSubObject: Map({
          subSubKey: 'subSubValue'
        })
      })
    })
    
    const newMap = originalMap.setIn(['subObject', 'subKey'], 'ha ha!')
    // Map {
    //   "subObject": Map {
    //     "subKey": "ha ha!",
    //     "subSubObject": Map { "subSubKey": "subSubValue" }
    //   }
    // }
    
    const newerMap = originalMap.setIn(
      ['subObject', 'subSubObject', 'subSubKey'],
      'ha ha ha!'
    )
    // Map {
    //   "subObject": Map {
    //     "subKey": "subvalue",
    //     "subSubObject": Map { "subSubKey": "ha ha ha!" }
    //   }
    // }
        
    
    ```
  
- `update()`

  - 更新数据

  - ```dart
    ////List
    const list = List([ 'a', 'b', 'c' ])
    const result = list.update(2, val => val.toUpperCase())
    
    ///Map
    const aMap = Map({ key: 'value' })
    const newMap = aMap.update('key', value => value + value)
        
    ```

## 关于merge

​	merge

作用：浅合并，新数据与旧数据对比，旧数据中不存在的属性直接添加，就数据中已存在的属性用新数据中的覆盖

###### mergrWith

作用：自定义浅合并，可自行设置某些属性的值

###### mergeIn

作用：对深层数据进行浅合并

###### mergeDeep

作用：深合并，新旧数据中同时存在的的属性为新旧数据合并之后的数据

###### mergeDeepIn

作用：对深层数据进行深合并

###### mergrDeepWith

作用：自定义深合并，可自行设置某些属性的值

这里用一段示例彻底搞懂merge，此示例为Map结构，List与Map原理相同

```dart
 const Map1 = Immutable.fromJS({a:111,b:222,c:{d:333,e:444}});
 const Map2 = Immutable.fromJS({a:111,b:222,c:{e:444,f:555}});

 const Map3 = Map1.merge(Map2);
  //Map {a:111,b:222,c:{e:444,f:555}}
 const Map4 = Map1.mergeDeep(Map2);
  //Map {a:111,b:222,c:{d:333,e:444,f:555}}
 const Map5 = Map1.mergeWith((oldData,newData,key)=>{
      if(key === 'a'){
        return 666;
      }else{
        return newData
      }
    },Map2);
  //Map {a:666,b:222,c:{e:444,f:555}}
```

---

更多API见

[github]: https://github.com/immutable-js/immutable-js








