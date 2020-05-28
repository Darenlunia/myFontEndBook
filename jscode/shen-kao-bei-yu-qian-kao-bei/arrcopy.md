---
description: 地址拷贝、浅拷贝、深拷贝。（数组也是对象，下面的例子以数组拷贝为主讲述，对象同）
---

# 深浅拷贝

#### 1. 指向同一个地址的拷贝：

修改arr的值=修改new\_arr的值。

```javascript
let a=[1,2,3]
let b=a
b[0]='*'
console.log(a)
console.log(b)
// [ '*', 2, 3 ]
// [ '*', 2, 3 ]
```

#### 2. 数组浅拷贝：

修改arr的值！=修改new\_arr的值。（第1层深拷贝）

但是a中有对象或者数组的话，修改a中数组/对象的值=修改b中数组/对象的值。（第n层浅拷贝）

解释：复制arr生成new\_arr的时候，arr和new\_arr指向的是不同地址，arr值类型数据直接复制值，而引用类型的数据复制的是地址。所以修改引用类型元素的时候会发生联动改变。

**语法：**

```javascript
let new_arr = arr.concat();//或者[].concat()也一样
let new_arr = arr.slice();
```

上面是常见语法，ES6还新增了一些浅拷贝方法：

```javascript
let new_arr = Array.from(arr)//数组
let new_arr = Object.assign({}, arr)//对象
let new_arr = [ ...arr ]//数组
let new_arr = { ...arr }//对象
```

**举例：**

```javascript
let arr = ['old', null, {old: 'old'}, ['old']];
let new_arr = arr.concat(); // 使用上面提到的浅拷贝方法
new_arr[0] = 'new';
arr[2].old = 'new';
new_arr[3][0] = 'new';
console.log(arr); 
console.log(new_arr); 
//[ 'old',{ name: 'wang' }, { old: 'new' }, [ 'new' ] ]
//[ 'new', null, { old: 'new' }, [ 'new' ] ]
```

未联动改变的：arr\[0\]和arr\[1\]；联动发生改变的：arr\[3\]和arr\[4\]，为啥arr\[1\]是对象发生了联动改变，因为arr修改值的时候直接修改了对象所指向的地址，而不是修改的对象值，因此new\_arr不跟随改变。

#### 3. 数组深拷贝

#### 3.1 比较简单的方法：

使用JSON的序列化与反序列化函数：

```javascript
var arr = ['old', ['old1', 'old2'], {old: 1}]
var new_arr = JSON.parse(JSON.stringify(arr))
arr[1][1]='new1'
arr[2].old=2
console.log(arr)
console.log(new_arr);
// [ 'old', [ 'old1', 'new1' ], { old: 2 } ]
// [ 'old', [ 'old1', 'old2' ], { old: 1 } ]
```

对于多数普通数组和对象是没有问题的，但也会有一些意外情况。因为JSON序列化的时候转换成字符串会进行一些类型转换。例如对象值中有函数、undefined、symbol等序列化后此类键值对会消失，Date 引用类型会变成字符串等。

#### 3.2 自定义拷贝函数：

核心：递归遍历赋值。

自定义深拷贝函数：（适用于所有数据类型进行深拷贝）

```javascript
function deepCopy(obj) {
    // 只拷贝对象
    if (typeof obj !== 'object') return;
    // 根据obj的类型判断是新建一个数组还是一个对象
    let newObj = obj instanceof Array ? [] : {};
    for (let key in obj) {
      // 遍历obj,并且判断是obj的属性才拷贝
      if (obj.hasOwnProperty(key)) {
        // 判断属性值的类型，如果是对象递归调用深拷贝
        newObj[key] = typeof obj[key] === 'object' ? deepCopy(obj[key]) : obj[key];
      }
    }
    return newObj;
}
```

对应的浅拷贝函数原理：（和上面函数差别只有一行，即第10行，赋值非深度递归赋值，而是直接赋值）

```javascript
function shallowCopy(obj) {
    // 只拷贝对象
    if (typeof obj !== 'object') return;
    // 根据obj的类型判断是新建一个数组还是一个对象
    let newObj = obj instanceof Array ? [] : {};
    // 遍历obj,并且判断是obj的属性才拷贝
    for (let key in obj) {
      if (obj.hasOwnProperty(key)) {
      //浅拷贝，引用类型直接赋值
        newObj[key] = obj[key];
      }
    }
    return newObj;
}
```

#### 3.3 lodash库中的深拷贝函数

```javascript
// 官方例子
var objects = [{ 'a': 1 }, { 'b': 2 }];
 
var deep = _.cloneDeep(objects);
console.log(deep[0] === objects[0]);
// => false
```

#### 4. 用“===”判断变量相等

拷贝值类型变量，直接用“===”判等没问题。拷贝引用类型变量后，如果是地址拷贝，则前后“===”相等；如果是浅拷贝，前后不相等；如果浅拷贝中有数组或对象，则他们不相等，但他们的的数组/对象前后“===”相等，因为指向相同地址；如果是深拷贝，无论几层引用类型，都不相等。

```javascript
let a=[1,2,3]
let b=[1,2,3]
console.log(a===b)//false
//地址拷贝
let c=a
console.log(a===b)//true
//浅拷贝
let d=a.concat()
console.log(a===d)//false
```

```javascript
//含对象元素的浅拷贝
let a=[1,{num:1}]
let b=a.concat()
console.log(a===b)//false
console.log(a[1]===b[1])//true
```

```javascript
//深拷贝
var objects = [{ 'a': 1 }, { 'b': 2 }];
var deep = _.cloneDeep(objects);
console.log(deep[0] === objects[0]);//false
```

