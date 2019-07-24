## 变量和类型

- 1.  js 规定了几种语言类型？

      > js 的六个基本数据类型（原始类型）为：String、Number、Boolean、undefined、null、Symbol（ES6） >js 基本类型都是直接按值存储在栈中的，每种数据类型占用的内存空间大小确定，由系统自动分配和自动释放。好处是内存可以及时回收，相对于堆来说更加容易管理内存空间

      > 一种复杂数据类型（引用类型） Object
      > 引用类型数据被存储于堆中(如对象、数组、函数等，通过拷贝和 new 出来的)

- 2. JavaScript 对象的底层数据结构是什么
     > 可查看 https://www.cnblogs.com/zhoulujun/p/10881639.html
     > js 对象的底层数据结构是地址指针存在栈中，数据存在堆中，通过指针去堆中查找到值
- 3. Symbol 类型在实际开发中的应用、可手动实现一个简单的 Symbol
     > Symbol 在实际中可用于定义一个唯一的对象属性，用于解决协同开发中的命名冲突
- 4. JavaScript 中的变量在内存中的具体存储形式
     > javascript 变量分为基本类型和引用类型  
     > 基本类型是保存在栈内存中的简单数据段，有固定大小，通过值来访问  
     > 引用类型是保存在堆内存中的对象，值大小不固定，将地址指针存在栈内存中，堆中存放值，通过指针找到值。js 不允许直接访问堆内存中的位置，因此操作对象时实际操作对象的引用
- 5. 基本类型对应的内置对象，以及他们之间的装箱拆箱操作
     > `String` 对象、`Math` 对象、正则表达对象、`Date` 对象、`Global` 对象、`Array` 对象  
     > 把基本数据类型转换为对应的引用类型的操作成为装箱，把引用类型转换为基本数据类型成为拆箱
     >
     > - 装箱：在基本类型上创建一个实例、在实例上调用制定方法、销毁实例
     > - 拆箱：引用类型转为对应的值类型，通过引用类型的 `valueOf()`或 `toString()`方法实现的。如果是自定义对象，也可以自定义它的`valueOf()`和 `toString()`方法
- 6. 理解值类型和引用类型
     >
- 7. null 和 undefined 的区别
     > null 表示一个空对象指针，typeof 返回 Object  
     > undefined 是一个已被声明还未被初始化的变量，用来表示尚未存在的对象。typeof 返回 undefined
     ```
     console.log(null == undefined) // true
     console.log(null === undefined)  // false
     ```

- 8.  至少可以说出三种判断 JavaScript 数据类型的方式，以及他们的优缺点，如何准确的判断数组类型

      > typeOf()

      - 对于基本类型除了 null 均可以返回正确的结果（String、number、boolean、undefined、Symbol）
      - 引用类型除了 function 外全部返回 Object
      - 对于 null 返回 object 类型，无法再返回具体类型
      - 对于 function 类型返回 function 类型

      > instanceOf()

      - 用来判断 A 是否为 B 的实例，表达式为：A instanceof B，是则返回 true 反之 false
      - instanceof 只能判断两个对象是否属于实例关系，而不能判断一个对象实例具体属于哪种类型

      > constructor

      - 刚刚

      > Object.prototype.toString  
      > jquery.type()  
      > 参考链接 https://www.cnblogs.com/onepixel/p/5126046.html  
      > https://blog.csdn.net/mozuncangtianbaxue/article/details/77151598

            Object.prototype.toString.call('') ; // [object String]
            Object.prototype.toString.call(1) ; // [object Number]
            Object.prototype.toString.call(true) ; // [object Boolean]
            Object.prototype.toString.call(undefined) ; // [object Undefined]
            Object.prototype.toString.call(null) ; // [object Null]
            Object.prototype.toString.call(new Function()) ; // [object Function]
            Object.prototype.toString.call(new Date()) ; // [object Date]
            Object.prototype.toString.call([]) ; // [object Array]
            Object.prototype.toString.call(new RegExp()) ; // [object RegExp]
            Object.prototype.toString.call(new Error()) ; // [object Error]
            Object.prototype.toString.call(document) ; // [object HTMLDocument]
            Object.prototype.toString.call(window) ; //[object global] window 是全局对象 global 的引用

- 9. 可能发生隐式类型转换的场景以及转换原则，应如何避免或巧妙应用
     > 隐式转换三种情况：
     - 转为 boolean 类型：数据在逻辑判断和逻辑运算之中会隐式转换为 boolean 类型
     - 转为 number 类型、转为 string 类型：主要是受到运行环境和操作符的影响
  - 当+号作为一元操作符操作单操作数的时候，他就会将这个数转换为数字类型
  - 当+号作为二元操作符时，如果两个操作数中存在一个字符类型的话，那么另外一个操作数也会无条件地转换为字符串
  - 当+号作为二元操作符时，如果两个操作数一个都不是字符串的话，两个操作数会隐式转换成数字类型(如果无法成功转换成数字，则变成 NaN，再往下操作)，再进行加法算数操作
  - 当算数运算的操作符是+号以外的其他操作数时，两个操作数都会转成数字类型进行数字运算。
  
  - 一个复杂对象在转为基础类型的时候会调用 ToPrimitive(hint)方法来指定其目标类型。如果传入的 hint 值为 number,那么就先调用对象的 valueOf()方法，调用完 valueOf()方法后，如果返回的是原始值，则结束 ToPrimitive 操作，如果返回的不是原始值，则继续调用对象的 toString()方法，调用完 toString()方法之后如果返回的是一个原始值，则结束 ToPrimitive 操作，如果返回的还是复杂值，则抛出异常。如果传入的 hint 值为 string，则先调用 toString()方法，再调用 valueOf()方法，其余的过程一样。
- 10. 出现小数精度丢失的原因，JavaScript 可以存储的最大数字、最大安全数字，JavaScript 处理大数字的方法、避免精度丢失的方法
  > 最大数字Math.pow(2,53)  
  > 避免精度丢失可以把小数放到位整数（乘背书），再缩小回原来倍数（除倍数）
```

```
