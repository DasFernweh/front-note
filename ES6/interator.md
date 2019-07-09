1. `js`原有的表示集合的数据结构主要是数组和对象，`ES6`添加了`Map`和`Set`
2. `Iterator`是一种接口，为各种不同的数据结构提供统一、简便的访问接口、使得数据结构的成员能够按某种次序排列、共`for...of`消费。本质是一个指针对象

```
var it = makeIterator(['a','b'])
it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

funciton makeIterator(array){
  var nextIndex = 0;
  return {
    next:function() {
      return nextIndex < array.length ? { value: array[nextIndex++], done: false} :{value: undefined, done:true}
    }
  }
}
```
