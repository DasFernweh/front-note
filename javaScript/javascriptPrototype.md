## 原型和原型链

1. 理解原型设计模式以及 JavaScript 中的原型规则

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
- 当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的*proto*（即它的构造函数的 prototype）中去寻找

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

2. instanceof 的底层实现原理，手动实现一个 instanceof
   https://www.jianshu.com/p/6c99d3678283
   > instanceof 可以判断一个引用是否属于某构造函数，还可以在继承关系中判断一个实例石佛属于他的父类型
   > instanceof 的判断逻辑是：从当前引用的 proto 一层一层顺着原型链往上找，能否找到对应的 prototype，找到了就返回 true
3. 实现继承的几种方式以及他们的优缺点
   > hh
4. 至少说出一种开源项目(如 Node)中应用原型继承的案例
   > hh
5. 可以描述 new 一个对象的详细过程，手动实现一个 new 操作符
   > hh
6. 理解 es6 class 构造以及继承的底层实现原理
   > hh
7. js 的设计模式（简单介绍）

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
