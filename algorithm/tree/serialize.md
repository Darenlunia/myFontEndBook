---
description: 二叉树的序列化和反序列化，是指数组和树结构之间的转化，一般用遍历的方法实现。这里采取的是先序遍历。
---

# 序列化与反序列化

#### 1. 树节点数据结构

```javascript
class TreeNode{
    constructor(val) {
        this.val=val
        this.left=null
        this.right=null
    }
}
```

#### 2. 二叉树序列化

即 树结构---&gt;数组或字符串。

```javascript
function Serialize(Root,arr=[]){
    if(Root===null){
        arr.push('#')
    }else{
        arr.push(Root.val)
        Serialize(Root.left,arr)
        Serialize(Root.right,arr)
    }
}
```

#### 3.二叉树反序列化

即 数组或字符串---&gt;树结构。

```javascript
function Deserialize(arr){
    if(!arr.length){return null}
    let node =null
    let cur = arr.shift()
    if(cur !=='#'){
        node=new TreeNode(cur)
        node.left=Deserialize(arr)
        node.right=Deserialize(arr)
    }
    return node
}
```

#### 4.测试

先测试反序列化，再使用反序列化生成的对象去进行序列化测试。

```javascript
//反序列化，输入数组，生成树
console.log(Deserialize([8,6,5,'#','#',7,'#','#',6,7,'#','#',5,'#','#']))

/**
 * 输出结果：
TreeNode {
  val: 8,
  right: TreeNode {
    val: 6,
    right: TreeNode { val: 5, right: null, left: null },
    left: TreeNode { val: 7, right: null, left: null }
  },
  left: TreeNode {
    val: 6,
    right: TreeNode { val: 7, right: null, left: null },
    left: TreeNode { val: 5, right: null, left: null }
  }
}
 */
```

```javascript
//序列化，输入树，生成数组
let root=
{
    val: 8,
    right: {
        val: 6,
        right:{ val: 5, right: null, left: null },
        left:{ val: 7, right: null, left: null }
    },
    left:{
        val: 6,
        right:{ val: 7, right: null, left: null },
        left:{ val: 5, right: null, left: null }
    }
}
let arr=[]
Serialize(root,arr)
console.log(arr)

/**
 * 输出：
[
  8,   6,   5,   '#', '#', 7,
  '#', '#', 6,   7,   '#', '#',
  5,   '#', '#'
]
*/
```

#### 5.总结

它们有个小特点，都是用了代码的同步性（对应异步代码），来完成先序遍历时父子节点的传递过程。

例如：

```javascript
node.left=Deserialize(arr)
node.right=Deserialize(arr)
```

这两句并非同步执行，而是left节点完成遍历（递归到临界值，最后得到null）后才到right节点执行语句。

之前还想使用堆栈来完成代码，把问题想复杂了，记住`代码逐行执行`的特点，可以少走弯路。

