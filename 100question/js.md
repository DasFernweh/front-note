1. 节流和防抖  
   防抖：触发高频事件后 n 秒内函数只会执行一次，如果 n 秒内高频事件再次被触发，则重新计算时间

```javascript
function debounce(fn,delay){
  let timeout = null; // 创建一个标记用来存放定时器的返回值
  return function(args) {
    clearTimeout(timeout); // 每当用户输入的时候吧前一个setTimeout clear掉
    timeout = setTimeout(()=>{
      // 然后又创建一个新的setTimeout，这样就能保证输入字符后的interval间隔内如果还有字符输入的话，就不会执行fn函数
      fn.apply(this,arguments)
    },500)
  }
}
funciton sayHi() {
  console.log('success')
}
var inp = document.getElementById('inp');
inp.addEventListener('input',debounce(sayHi)); // 防抖
```

节流：高频事件触发，但在 n 秒内只会执行一次，所以节流会稀释函数的执行频率

```javascript
function throttle(fn) {
  let canRun = true // 通过闭包保存一个标记
  return function() {
    if (!canRun) return // 在函数开头判断标记是否为true，不为 true 则 return
    canRun = false
    setTimeout(() => {
      fn.apply(this, arguments)
      canRun = true // 最后在setTimeout执行完毕后再把标记设置为true表示可以执行下一次循环
      // 当定时器没有执行的时候标记永远是false，开头被return掉
    }, 500)
  }
}
function sayHi(e) {
  console.log(e.target.innerWidth, e.target.innerHeight)
}
window.addEventListener('resize', throttle(sayHi))
```

2. 深度优先遍历和广度优先遍历

- 深度优先遍历：假设初始状态是图中所有顶点均未被访问，则从某个顶点 v 出发，首先访问该顶点然后依次从它的各个未被访问的邻接点出发深度优先搜索遍历图，直至图中所有和 v 有路径相通的顶点都被访问到。若此时尚有其他顶点未被访问到，则另选一个未被访问的顶点作起始点，重复上述过程，直至图中所有顶点都被访问到为止。

```javascript
// 三种方式实现深度优先遍历
let deepTraversal1 = (node, nodeList = []) => {
  if (node !== null) {
    nodeList.push(node)
    let children = node.children
    for (let i = 0; i < children.length; i++) {
      deepTraversal1(children[i], nodeList)
    }
  }
  return nodeList
}

let deepTraversal2 = node => {
  let nodes = []
  if (node !== null) {
    nodes.push(node)
    let children = node.children
    for (let i = 0; i < children.length; i++) {
      nodes = nodes.concat(deepTraversal2(children[i]))
    }
  }
  return nodes
}

// 非递归
let deepTraversal3 = node => {
  let stack = [],
    nodes = []
  if (node) {
    stack.push(node)
    while (stack.length) {
      let item = stack.pop()
      let children = item.children
      nodes.push(item)
      for (let i = children.length - 1; i >= 0; i--) {
        stack.push(children[i])
      }
    }
  }
  return nodes
}
```

- 广度优先遍历：从图中某顶点 v 出发，在访问了 v 之后依次访问 v 的各个未曾访问过的邻接点，然后分别从这些邻接点出发依次访问它们的邻接点，并使得“先被访问的顶点的邻接点先于后被访问的顶点的邻接点被访问，直至图中所有已被访问的顶点的邻接点都被访问到。 如果此时图中尚有顶点未被访问，则需要另选一个未曾被访问过的顶点作为新的起始点，重复上述过程，直至图中所有顶点都被访问到为止。

```javascript
// 递归
function wideTraversal1(node) {
  let nodes = [],
    i = 0
  if (node != null) {
    nodes.push(node)
    wideTraversal(node.nextElementSibling) // nextSibling属性返回指定节点之后的下一个兄弟节点
    // nextSibling返回元素节点之后的兄弟节点（包括文本节点、注释节点即回车、换行、空格、文本等等）
    // nextElementSibling属性只返回元素节点之后的兄弟元素节点（不包括文本节点、注释节点）
    node = nodes[i++]
    wideTaversal(node.firstElementChild)
  }
  return nodes
}
// 非递归
function wideTraversal2(node) {
  let nodes = [],
    i = 0
  while (node != null) {
    nodes.push(node)
    node = nodes[i++]
    let childrens = node.children
    for (let i = 0; i < childrens.length; i++) {
      nodes.push(childrens[i])
    }
  }
  return nodes
}

functin wideTraversal(node){
  var nodes =[]
  if(node!=null){
    var queue = [];
    queue.unshift(node); // 向数组的开头添加一个或更多元素，并返回新的长度
    while(queue.length!=0){
      var item = queue.shift(); // 删除并返回数组的第一个元素
    }
  }
}
```

3. 实现对象深拷贝

```javascript
// 工具函数
// Object对象和它的原型链上各自有一个toString()方法，第一个返回的是一个函数，第二个返回的是值类型
const _toString = Object.prototype.toString() // [object Object] 检测类型
function getType(obj) {
  return _toString.call(obj).slice(8, -1) // 截取字符串[object 到 ] 去掉前后符号
}
// 深度优先
function DFSDeepClone(obj, vistied = new Set(), level = 0) {
  let res = {}
  if (getType(obj) === 'Object' || getType(obj) === 'Array') {
    if (vistied.has(obj)) {
      // 处理环状结构
      res = obj
    } else {
      vistied[level] = obj
      vistied.add(obj)
      res = getType(obj) === 'Object' ? {} : []
      Object.keys(obj).forEach(k => {
        res[k] = DFSDeepClone(obj[k], vistied, level + 1)
      })
    }
  } else if (typeof obj === 'function') {
    res = eval(`(${obj.toString()})`)
    // eval(string) 计算某个字符串，并执行其中的javascript代码；string要计算的字符串，其中含有要计算的 JavaScript 表达式或要执行的语句
  } else {
    res = obj
  }
  return res
}
// 广度优先
```

4. 异步。await 是让出线程的标志。await 后面的表达式先执行一遍，将后面的代码加入到 microtask 中，然后就会跳出整个 async 函数来执行后面的代码
   https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/7

```javascript
async function async1() {
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2')
}
console.log('script start')
setTimeout(function() {
  console.log('setTimeout')
}, 0)
async1()
new Promise(function(resolve) {
  console.log('promise1')
  resolve()
}).then(function() {
  console.log('promise2')
})
console.log('script end')
// 打印结果
script start
async1 start
promise1
promise3
script end
promise2
async1 end

promise4
setTimeout

正确答案
VM104:9 script start
VM104:2 async1 start
VM104:7 async2
VM104:15 promise1
VM104:20 script end
VM104:4 async1 end
VM104:18 promise2
undefined
VM104:11 setTimeout
```

5. 数组去扁平化并去重

```javascript
// 扁平化
var arr = [[1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14]]]], 10]
function flatten(arr) {
  return arr.reduce((result, item) => {
    return result.concat(Array.isArray(item) ? flatten(item) : item)
  }, [])
}

Array.from(new Set(arr.flat(Infinity))).sort((a, b) => {
  return a - b
}) 
// flat()将嵌套的数组拉平，多层嵌套可以设置flat的参数
// Array.from()将两类对象转为真正的数组：累死数组的对象和可遍历的对象（如Set和Map）
```
