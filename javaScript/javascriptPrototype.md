## 原型和原型链

1.  理解原型设计模式以及 JavaScript 中的原型规则

    - https://blog.csdn.net/weixin_34149796/article/details/91472253
    - 所有对象都有的私有对象 prototype，就是对象的原型
    - 读取一个对象的属性，如果对象本身没有，就是到对象的原型上找，直到原型为空或者找到这个属性为止
    - 原型模式：通俗讲是创建一个共享的原型，并通过拷贝这些原型创建新的对象。用于创建重复的对象，这种类型的设计模式属于创建型模式（带来性能上的提升）
    - 原型模式实现：通过 js 特有的原型继承性去实现原型模式，也就是创建一个对象作为另一个对象的 prototyp 属性值，可以通过 object.create(prototype,optionalDescriptorObjects)来实现原型继承

    ES6 中访问和操作原型的方式：`Object.create` `Object.getPrototypeof` `Object.setPrototypeof`

    原型规则：

    - 所有的引用类型（数组、对象、函数），都具有对象特性，即可自由扩展属性

      ```javascript
      var arr = []
      arr.a = 1
      ```

    - 所有的引用类型（数组、对象、函数）都有`_proto_`属性（隐式原型），属性值是一个普通对象
    - 所有的函数都具有一个 prototype（显式原型），属性值是一个普通对象
    - 所有的引用类型（数组、对象、函数），其隐式原型指向其构造函数的显示原型（`obj._proto_===Object.prototype`）
    - 当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的`__proto__`（即它的构造函数的 prototype）中去寻找

      原型的作用就是给这个类的每一个对象都添加一个统一的方法，在原型中定义的方法和属性都是被所有实例对象所共享

      ```javascript
      var someCar = {
        drive: function() {},
        name: '奔驰大G',
      }
      var anotherCar = object.create(someCar) // Object.create会使用指定的原型对象及属性去创建一个新的对象
      anotherCar.name = '丰田佳美'
      ```

      ```javascript
      var vehicle = {
        getModel: function() {
          console.log('车辆模具是：' + this.model)
        },
      }
      var car = Object.create(vehicle, {
        id: {
          value: MY_GLOBAL.nextId(),
          enumerable: true,
        },
        model: {
          value: '福特',
          enumerable: true,
        },
      })
      ```

      如果你希望自己去实现原型模式，而不直接使用 Object.create。你可以使用以下代码实现

      ```javascript
      var vehiclePrototype = {
        init: function(carModel) {
          this.model = carModel
        },
        getModel: function() {
          console.log('车辆模具是：' + this.model)
        },
      }
      function vehicle(model) {
        function F() {}
        F.prototype = vehiclePrototype
        var f = new F()
        f.init(model)
        return f
      }
      var car = vehicle('福特Escort')
      car.getModel()
      ```

2.  instanceof 的底层实现原理，手动实现一个 instanceof
    https://www.jianshu.com/p/6c99d3678283
    > instanceof 可以判断一个引用是否属于某构造函数，还可以在继承关系中判断一个实例石佛属于他的父类型
    > instanceof 的判断逻辑是：从当前引用的 proto 一层一层顺着原型链往上找，能否找到对应的 prototype，找到了就返回 true
    ```javascript
    // 手动实现insranceof
    // 直接使用instance of规则
    function instance_of(L, R) {
      var O = R.prototype
      L = L._proto_
      while (true) {
        if (L === null) {
          return false
        }
        if (O === L) {
          return true
        }
        L = L._proto_
      }
    }
    // 在以上方法的基础上使用constructor（此方法无法用于判断继承）
    function indtance_of(L, R) {
      var O = R
      L = L._proto_
      while (true) {
        if (L === null) return false
        if (O === l.constructor) return true
        L = L._proto_
      }
    }
    ```
3.  实现继承的几种方式以及他们的优缺点
    https://www.cnblogs.com/yaphetsfang/p/9376770.html  
    https://blog.csdn.net/longyin0528/article/details/80504270

    - 原型链继承  
       特点：

      - 非常纯粹的继承关系，实例都是子类的实例，也都是父类的实例
      - 父类新增的原型方法/原型属性，子类都能访问到
      - 简单，易于实现

      缺点：

      - 要想为子类新增属性和方法，必须在继承语句之后执行
      - 无法实现多继承
      - 构造函数原型上的属性在所有该构造函数构造的实例上是共享的，即属性没有私有化，原型上属性的改变会作用到所有的实例上
      - 创建子类实例时，无法向父类构造函数传参

        ```javascript
        // 父类，带属性
        function Super() {
          this.falg = true
        }
        // 为了提高复用性，方法绑定在父类原型属性上
        Super.prototype.getFlag = function() {
          return this.flag
        }
        // 子类
        function Sub() {
          this.subFlag = false
        }
        Sub.prototype = new Super() // 实现继承
        // 给子类添加子类特有的方法，注意顺序要在继承之后
        Sub.prototype.getSubFlag = function() {
          return this.subFalg
        }
        ```

    - 构造函数继承  
       核心：使用父类的构造函数来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）  
       特点：

      - 解决了子类实例共享父类引用属性的问题
      - 创建子类实例时可以向父类传递参数
      - 可以实现多继承（call 多个父类对象）

      缺点：

      - 实例并不是父类的实例，只是子类的实例
      - 只能继承父类的实例属性和方法，不能继承原型属性/方法
      - 无法实现函数复用，每个子类都有父类实例函数的副本，影响性能

      ```javascript
      function Animal(name) {
        this.name = name || 'Animal'
        this.sleep = function() {
          console.log(this.name + ' is sleeping')
        }
      }

      Animal.prototype.eat = function(food) {
        console.log(this.name + ' eating ' + food)
      }

      function Cat(name) {
        Animal.call(this)
        this.name = name || 'Tom'
      }
      var cat = new Cat()
      console.log(cat.name) // Tom
      console.log(cat.sleep()) // Tom is sleeping
      console.log(cat.eat()) // undefined 只能继承父类的实例属性和方法，不能继承原型属性/方法
      console.log(cat instanceof Animal) // false
      console.log(cat instanceof Cat) // true
      ```

    - 实例继承  
       核心：为父类实例添加新特性，作为子类实例返回  
       特点：

      - 不限制调用方式，不管是 new 子类()还是子类()，返回的对象具有相同的效果

      缺点：

      - 实例是父类的实例，不是子类的实例
      - 不支持多继承

      ```javascript
      function Cat(name) {
        var instance = new Animal()
        instance.name = name || 'Tom'
        return instance
      }

      var cat = new Cat()
      console.log(cat.name)
      console.log(cat.sleep())
      // instanceof左值一般是一个对象，右值一般是一个构造函数用来判断左值是否是右值的实例。它的实现原理是沿着左值的__proto__一直寻找到原型链的末端，直到其等于右值的prototype为止
      console.log(cat instanceof Animal) // true
      console.log(cat instanceof Cat) // false  ？？？？？
      ```

    - 拷贝继承  
      特点：

           - 不限制调用方式，不管是 new 子类()还是子类()，返回的对象具有相同的效果

      缺点：

           - 实例是父类的实例，不是子类的实例
           - 不支持多继承

      ```javascript
      function Cat(name) {
        var animal = new Animal()
        for (var p in animal) {
          Cat.prototype[p] = animal[p]
        }
        Cat.prototype.name = name || 'Tom'
      }

      var cat = new Cat()
      console.log(cat.name)
      console.log(cat.sleep()) // Tom is sleeping
      console.log(cat instanceof Animal) // false
      console.log(cat instanceof Cat) // true
      ```

    - 组合继承(伪经典继承)  
      核心：将原型链和借用构造函数的技术组合到一块，使用原型链实现原型属性和方法的集成，通过构造函数来实现对实例属性的继承。  
      通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型实现函数复用

      特点：

      - 弥补了构造函数继承的缺陷，可以继承实例属性/方法，也可以继承原型属性/方法
      - 既是子类的实例，也是父类的实例
      - 不存在引用属性共享问题
      - 可传参
      - 函数可复用

      缺点：

      - 调用了两次父类构造函数，生成了两份实例（子类实例将子类原型上的那份屏蔽了）

      ```javascript
      function Cat(name) {
        // 继承属性
        Animal.call(this, name)
        this.name = name || 'Tom'
      }
      // 继承方法
      Cat.prototype = new Animal()
      Cat.prototype.constructor = Cat // 修复构造函数指向

      var cat = new Cat()
      console.log(cat.name)
      console.log(cat.sleep())
      console.log(cat instanceof Animal) // true
      console.log(cat instanceof Cat) // true
      ```

    - 寄生组合继承  
      核心：通过寄生方式，砍掉父类的实例属性，这样在调用两次父类的构造的时候，就不会初始化两次实例方法/属性，避免组合继承的缺点
      特点：

      - 堪称完美

      缺点：

      - 实现较为复杂

      ```javascript
      funciton Cat(name) {
        Animal.call(this);
        this.name = name || 'Tom';
      }

      (function() {
        var Super = function() {};
        Super.prototype = Animal.prototype;
        Cat.prototype = new Super();
      })();

      var cat = new Cat();
      console.log(cat.name);
      console.log(cat.sleep());
      console.log(cat instanceof Animal); // true
      console.log(cat instanceof Cat); //true
      ```

    - ES6 class(和寄生继承实现的效果一致)

      ```javascript
      class Parent5 {
        constructor() {
          this.name = ['super5']
        }
        reName() {
          this.name.push('new 5')
        }
      }
      class Child5 extends Parent5 {
        constructor() {
          super()
        }
      }

      var child51 = new Child5()
      var child52 = new Child5()
      child51.reName()
      console.log(child51.name, child52.name) // [ 'super5', 'new 5' ], 子类实例不会相互影响
      console.log(child51.reName === child52.reName) //true, 共享了父类的方法
      ```

4.  至少说出一种开源项目(如 Node)中应用原型继承的案例
    > hh
5.  可以描述 new 一个对象的详细过程，手动实现一个 new 操作符

    > 参考地址：https://blog.csdn.net/cc18868876837/article/details/81211729
    > 详细过程：

    - 创建一个新对象
    - 将所创建对象的`_proto_`属性值设为构造函数的`prototype`的属性值
    - 执行构造函数中的代码，构造函数中的 this 指向该对象
    - 返回对象

    ```javascript
    function new1(func) {
      var newObj = Object.create(func.prototype); // 创建一个继承自func.prototype的新对象
      // 截取new1函数第二个以及第二个之后的参数，在newObj作用域内执行改造函数func
      var returnObj = func.apply(newObj,Array.prototype.slice.call(arguments,1));
      // 如果传入参数中的构造函数执行后的returnObj是对象类型（比如new1(Object)），那么这个对象会取代newObj作为返回的对象
      if((typeof returnObj === 'object' || typeof returnObj === 'function') && return !== null) {
        return returnObject;
      } //如果传入参数中的构造函数执行后的returnObj是“对象”类型(比如new1(Object)),那么这个对象会取代newObj作为返回的对象
      return newObj
    }

    fucntion new2(func){
      return function() {
        let newObj = {
          __proto__:func.prototype  // 新生成一个对象，且新对象的原型对象继承自构造对象的原型对象
        }
        var returnObj = func.apply(obj,arguments)  // 以第二次执行函数的参数，在obj作用域中执行func
        if((typeof returnObj === 'object' || typeof returnObj === 'function') && returnObj !== null){
          return returnObj
        }
        return newObj
      }
    }

    var object1 = new1(Object);
    var object2 = new2(Object)();
    var object3 = new Object();

    function person(name,age){
      this.name = name
      this.age = age
    }

    var func1 = new1(person,'zhus',25)
    var func2 = new2(person)('zhus',25)
    var func3 = new person('zhus',25)
    ```

6.  理解 es6 class 构造以及继承的底层实现原理

    > es6 的 class 可以看成一个语法糖  
    > `obj.hasOwnProperty`用来检测一个对象是否含有特定的自身属性，不包含继承属性。在 prototype 上边的是不能访问到的  
    > 类中的静态方法只能通过类来调用，不能被实例继承

    ```javascript
    class Point {}
    typeof Point // function
    Point === Point.prototype.constructor
    // 类是数据类型就是函数，类本身就指向构造函数
    ```

7.  js 的设计模式（简单介绍）

    - 工厂模式：在函数内创建一个对象，给对象赋予属性及方法再将对象返回

    ```javascript
    function Person() {
      var Peopel = new Object()
      People.name = 'CrazyLee'
      People.age = '25'
      Peopel.sex = function() {
        return 'boy'
      }
      return People
    }
    var a = Person()
    console.log(a.name)
    console.log(a.sex())
    ```

    - 构造函数模式：无需再函数内部重新创建对象，而是用 this 指代

    ```javascript
    funciton Person() {
      this.name = 'CrazyLee';
      this.age = '25'
      this.sex = function() {
        return 'boy'
      }
    }
    var a = new Person()
    console.log(a.name)
    console.log(a.sex())
    ```

    - 原型模式：函数中不对属性进行定义，利用 prototype 属性对属性进行定义，可以让所有对象实例共享他所包含的属性及方法

    ```javascript
    function Parent() {
      Parent.prototype.name = 'crazy'
      Parent.prototype.age = '24'
      Parent.prototype.sex = function() {
        var s = '女'
        console.log(s)
      }
    }
    var x = new Parent()
    console.log(x.name)
    console.log(x.sex())
    ```

    - 混合模式：原型模式+构造函数模式。在这种模式中，构造函数模式用于定义实例属性，而原型模式用于定义方法和共享属性

    ```javascript
    funciton Parent() {
      this.name = 'CrazyLee';
      this.age = 24;
    }
    Parent.prototype.sayname = function() {
      return this.name;
    }
    var x = new Parent();
    console.log(x.sayname())
    ```

    - 动态原型模式：将所有的信息封装在了构造函数中，而通过构造函数中初始化原型，这个可以通过判断该方法是否有效而选择是否需要初始化原型

    ```javascript
    funciton Parent() {
      this.name = 'CrazyLee';
      this.age = 24;
      if(typeof Parent._sayname == 'undefined'){
        Parent.prototype.sayname = funciton() {
          return this.name;
        }
        Parent._sayname = true
      }
      var x = new Parent();
      console.log(x.sayname())
    }
    ```

8.  js 的原型和原型链的理解  
     https://www.cnblogs.com/zhoulujun/p/9667651.html
    - prototype：构造函数中的属性，指向该构造函数的原型对象
    - consturctor：原型对象中的属性，指向该原型对象的构造函数
    - `__proto__`：实例中是属性，指向 new 这个实例的构造函数的原型对象
