---
description: 解决八皇后问题，可以分为两个层面：1.找出第一种正确摆放方式，也就是深度优先遍历。2.找出全部的正确摆放方式，也就是广度优先遍历。
---

# 八皇后问题

#### 1. 解题思路：

考虑前面提到的全排问题，其实就是一个3\*3矩阵。而这里换成了8\*8矩阵而已。方法还是那个方法，只不过多加了`任意两皇后不能在同一个斜边上`的限制条件。

#### 2. 代码

第一部分：循环嵌套递归，完成回溯

```javascript
/**
 * @param {计数，递归到第几行了} row 
 * @param {记录递归到当前行矩阵状态} current 
 */
function findNext(row=0,current){ 
    if(row===8){
        console.log(current)
        return
    }
    for(let col=0;col<8;col++){ 
        current[row][col]='Q'//做选择
        if(isValid(current,row,col)){//有效才下一行（剪枝）
            let temp=JSON.parse(JSON.stringify(current))//深拷贝
            findNext(row+1,temp)//进行下一选择
        }
        current[row][col]='.'//取消选择
    }
}
```

第二部分：设置限制条件函数

```javascript
/**
 * 保证当前矩阵最后一行的Q，位置放置符合要求
 */
function isValid(cur,row,col){
    //判断同列
    for(let i=0;i<row;i++){
        if(cur[i][col]==='Q')
        return false
    }
    //判断左上方
    for(let i=row-1,j=col-1;i>=0&&j>=0;i--,j--){
        if(cur[i][j]==='Q'){
            return false
        }
    }
    //判断右上方
    for(let i=row-1,j=col+1;i>=0&&j<=8;i--,j++){
        if(cur[i][j]==='Q'){
            return false
        }
    }
    return true
}
```

第三部分：测试代码

```javascript
let board=new Array(8)
for(let i=0;i<8;i++){
    board[i]=new Array(8)
    board[i].fill('.')
}
findNext(0,board)
```

#### 3. 拓展

这里是直接输出了所有结果，如果只要找到一种结果即可停止，可以在回溯函数中进行判断返回，如下：

```javascript
function findNext(row=0,current){ 
    if(row===8){
        console.log(current)
        return true
    }
    for(let col=0;col<8;col++){ 
        current[row][col]='Q'//做选择
        if(isValid(current,row,col)){//有效才下一行（剪枝）
            let temp=JSON.parse(JSON.stringify(current))//深拷贝
            // findNext(row+1,temp)//进行下一选择
            if(findNext(row+1,temp)){return true}
        }
        current[row][col]='.'//取消选择
    }
}
```

修改了第4、10行，新增第11行。

#### 4. 总结

 写 `backtrack` 函数（也就是findNext函数）时，**需要维护走过的「路径」和当前可以做的「选择列表」，当触发「结束条件」时，将「路径」记入结果集**。

再次啰嗦一句：回溯即循环嵌套递归。每次循环中执行了：做选择-&gt;判断是否符合条件-&gt;深拷贝-&gt;进行下一轮选择（递归）-&gt;取消选择。这个过程是不变的。

#### 5. 鄙人参考文献：

1. [帮助理解题目的图画](https://juejin.im/post/5accdb236fb9a028bb195562)（代码有问题不推荐参考）

2. [从全排到八皇后](https://juejin.im/post/5e5b0716518825496452b6cf)

3. [LeetCode八皇后问题](https://leetcode-cn.com/problems/n-queens/solution/hui-su-suan-fa-xiang-jie-by-labuladong/)（大佬总结回溯问题，重点参考）



