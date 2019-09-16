## 数据结构和算法

1. 内部排序

- 冒泡排序

```javascript
function bubbleSort(arr) {
  let len = arr.length
  for (var i = 0; i < len; i++) {
    for (var j = 0; j < len - 1 - i; j++) {
      let a = arr[j],
        b = arr[j + 1]
      if (a > b) {
        arr[j] = b
        arr[j + 1] = a
      }
    }
  }
  return arr
}
var arr = [4, 8, 1, 2, 5, 9, 44, 12, 35, 89]
bubbleSort(arr)
//  改进1：设置一标志性变量pos，记录每趟排序中最后一次进行交换的位置
function bubbleSort2(arr) {
  let i = arr.length - 1 // ????为什么要减1
  while (i > 0) {
    let pos = 0
    for (var j = 0; j < i; j++) {
      if (arr[j] > arr[j + 1]) {
        pos = j // 记录交换位置
        let tmp = arr[j]
        arr[j] = arr[j + 1]
        arr[j + 1] = tmp
      }
    }
    i = pos
  }
  return arr
}

function bubbleSort3(arr) {
  let low = 0,
    high = arr.length - 1
  let tmp, j
  while (low < high) {
    // 正向冒泡
    for (j = low; j < high; j++) {
      if (arr[j] > arr[j + 1]) {
        tmp = arr[j]
        arr[j] = arr[j + 1]
        arr[j + 1] = tmp
      }
    }
    --high
    for (j = high; j > low; j--) {
      if (arr[j] < arr[j - 1]) {
        tmp = arr[j]
        arr[j] = arr[j - 1]
        arr[j - 1] = tmp
      }
    }
    ++low
    // 反向冒泡
  }
  return arr
}
// 改进2：传统冒泡排序中每次只能找到一个最大值或最小值，考虑在每趟排序中进行正向和反向两遍冒泡方法使得一次可以得到两个最终值
// 从而使排序趟数几乎减少一半
```

- 选择排序

```javascript
function selectionSort(arr) {
  let len = arr.length
  let temp, minIndex
  for (i = 0; i < len; i++) {
    minIndex = i
    // 注意j的初始值设置
    for (j = i + 1; j < len - 1; j++) {
      // 也可实现排序，但时间长
      if (arr[minIndex] > arr[j]) {
        temp = arr[minIndex]
        arr[minIndex] = arr[j]
        arr[j] = temp
      }
    }
  }
  return arr
}

function selectionSort(arr) {
  let len = arr.length
  let temp, minIndex
  for (i = 0; i < len; i++) {
    minIndex = i
    // 注意j的初始值设置
    for (j = i + 1; j < len - 1; j++) {
      // 寻找最小数，保存他的索引
      if (arr[minIndex] > arr[j]) {
        minIndex = j
      }
    }
    temp = arr[i]
    arr[i] = arr[minIndex]
    arr[minIndex] = temp
  }
  return arr
}
```
