---
description: >-
  const声明的对象，不变的是地址还是对象值？传参const变量是否会受到影响？传参涉及到变量、数组、对象、回调函数，传参的时候传的究竟是地址还是值？实参赋值是浅拷贝还是深拷贝？在此做一总结。
---

# 值类型和引用类型赋值

值类型变量，变量存储的是值；引用类型变量，变量存储的是对象所在的地址。这个概念是应当熟知的基础，这里复习一遍，但不再提及与重复。

举例：

```javascript
let a=[1,2,3]
let b=[1,2,3]
console.log(a===b)//false
let c=a.concat()
console.log(a===c)//false
```

需要记住的只是引用类型的变量指向的是对象（包括数组）的地址。

下面写一些我容易犯混的地方，还是拿例子说话。

#### 1.函数传参-值类型

直接拷贝实参值传入形参，不影响外部变量。

```javascript
function f(val){
    val++
   return val 
}
let a=10
let val=f(a)
console.log(a)
console.log(val)
// 10
// 11
```

#### 2.函数传参-引用类型

传入的是对象变量地址，对其进行修改会影响外部变量。

```javascript
function f(obj){
    obj.name="wang"
    return obj
}
let a={
    name:"liu"
}
let obj=f(a)
console.log(a)
console.log(obj)
// { name: 'wang' }
// { name: 'wang' }
```

#### 3. const 声明引用类型

const基础：不可修改所声明变量指向的值。

```javascript
// const a=10
// a=20 报错

// const b={name:"wang"}
// b={name:"liu"} 报错

const b={name:"wang"}
b.name="liu"
console.log(b)//{ name: 'liu' }
```

const声明变量是引用类型时，不可变的只是变量绑定的指针（变量的值是栈中存放的内存地址）。如果直接给const声明变量赋值新对象，是换指针，将被禁止。如果修改对象的属性，不会换指针，是被允许的。

由此可见，const关键字并不能定义真正的常量对象，真正的常量对象应该是：不可扩展、不可删除、不可修改的。这一特点可以使用 \`Object.freeze\(obj\)\` 来实现。冻结后该对象不可扩展、不可删除、不可修改，并且不能修改该对象已有属性的可枚举性、可配置性、可写性。该方法修改原对象，并返回该对象。

#### 4. 函数传参-const声明值类型

```javascript
function f1(val){
    val++
    return val
}
const a=10
let val=f1(a)
console.log(a)
console.log(val)
// 10
// 11
```

val作为形参被赋值了实参a的值，但不会继承a的const性质，因此可以修改自己的值。

#### 5.函数传参-const声明引用类型

```javascript
function f1(obj){
    obj.name="wang"
    return obj
}
const b={name:"liu"}
let obj=f1(b)
console.log(b)
console.log(obj)
// { name: 'wang' }
// { name: 'wang' }
```

可以看到obj和b指向的是同一个地址，同一个对象。

#### 6. 函数传参-const声明引用类型-改值

```javascript
function f1(obj){
    obj={name:"wang"}
    return obj
}
const b={name:"liu"}
let obj=f1(b)
console.log(b)
console.log(obj)
// { name: 'liu' }
// { name: 'wang' }
```

可以看到obj形参一开始指向实参b的对象地址，虽然b是const不能指向新的对象，但形参不是const类型，因此可以指向新的对象，给obj赋值“wang”不会报错。

#### 7. 总结

其实本处讲的const声明引用类型是很基础的知识点，只不过加入到了传参中。其实只要知道const引用类型可以修改属性，不可以修改指针，就不会对const的应用场景再产生错误判断了。

再拓展一个非const声明的对象赋值，容易犯错的例子（好像只有我会容易犯错 = =）：

```javascript
let a={name:"wang"}
let b=a
a=null
console.log(b)
// { name: 'wang' }
```

如果修改a的属性，a和b指向同一个地址。如果修改a的对象，也就是赋值新对象，a指向的地址发生改变，a指向的是null对象所在地址；而b指向的地址不会随着a而改变，所以还是指向“wang”。

这还是个修改属性 or 赋值新对象的问题。要记住赋值新对象是修改对象地址，而不是在原地址覆盖新对象，就不会错了。



