---
description: 动态规划入门，理解“子问题重叠”。Fibonacci，动态规划的基础题型
---

# 斐波那契数列

#### 1. 题目理解

Fibonacci本身就是一个递归式：

```javascript
n=0   ==>   Fibonacci(n)=1; 
n=1   ==>   Fibonacci(n)=1;
n     ==>   Fibonacci(n-1)+Fibonacci(n-2)
```

其他问题都是要自行抽象出这种递推式，下面来看这个基础递归式的解法。

#### 2. 基础解法 

```javascript
//解法一：递归法
function fib1(n){
    if(n<=1){
        return 1
    }else{
        return fib1(n-1)+fib1(n-2)
    }
}
```

使用了递归穷举的方法。假设输入的n是6，则题解过程如下：

![](../../.gitbook/assets/tu-pian-%20%284%29.png)

由此可见，只fib\(2\)就被执行了5次。如果执行时把执行过的子节点保存起来，后面要用的时候直接查表，可以节省时间。

#### 3. 优化穷举方法

用空间换时间，用数组记录已经计算过的值。自顶向下的方法是“备忘录”法，自底向上的方法是“Dp table”法。

（1）自顶向下

```javascript
//解法二：备忘录法-自顶向下
//声明数组 存放已经计算过的fib值，memo的长度应为n
let memo=[]
memo[0]=0
memo[1]=1
memo[2]=1
function fib2(n){
    // 如果备忘录中已经存在则直接取出
    if(memo[n]){
        return memo[n]
    }else{
        //否则计算备忘录中的数据并返回这个数据
        memo[n]=fib2(n-1)+fib2(n-2)
        return memo[n]
    }
}
```

（2）自底向上

```javascript
//解法三：备忘录法-自底向上
let memo=[]
memo[0]=0
memo[1]=1
memo[2]=1
function fib3(n){
    if(n<=2){
        return memo[n]
    }else{
        for(let i=3;i<=n;i++){
            memo[i]=memo[i-1]+memo[i-2]
        }
    }
    return memo[n]
}
```

```javascript
//解法四：进一步压缩解法三的空间
function fib4(n){
    let memo1=1
    let memo2=1
    if(n<=1){
        return memo[n]
    }else{
        for(let i=3;i<=n;i++){
            memo_i=memo1+memo2
            memo1=memo2
            memo2=memo_i
        }
    }
    return memo_i
}
```
