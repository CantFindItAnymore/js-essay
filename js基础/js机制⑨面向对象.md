### 一. 概述

#### 1. 面向对象的定义

对象被定义为**“无序属性的集合，其属性可以包含基本值，对象或者函数”**。 

#### 2. 几个例子

- jQuery的面向对象实现
- 封装拖拽
- 简易版运动框架封装



------

### 二. 详述对象

```javascript
// 这里的person就是一个对象
var person = {
    name: 'Tom',
    age: 18,
    getName: function() {},
    parent: {}
}
```

 #### 1. 创建一个对象

```javascript
// 方法1
var obj = new Object()

// 方法2
var obj = {}
```

#### 2. 为对象添加方法

```javascript
// 方法1
var person = {};
person.name = "TOM";
person.getName = function() {
    return this.name;
}

// 方法2
var person = {
    name: "TOM",
    getName: function() {
        return this.name;
    }
}
```

#### 3.访问对象的属性和方法

```javascript
var person = {
    name: 'TOM',
    age: '20',
    getName: function() {
        return this.name
    }
}

// 可以这样访问↓

// 方法1
person.name

// 方法2
person['name'] // 这种方法在属性名为变量时经常用到，很重要哟~
```

#### 4. 工厂模式

在以上的例子中，我们定义了一个name为TOM的对象，没毛病，可是实际开发中我们往往需要很多个person对象。每次都去自己写吗？NONONO。

工厂模式即可解决这个问题。

可以这么理解，工厂模式为我们提供了一个模板，我们照着这个模板复制出要的实例。例如↓

```javascript
var createPerson = function(name, age) {

    // 声明一个中间对象，该对象就是工厂模式的模子
    var o = new Object();

    // 依次添加我们需要的属性与方法
    o.name = name;
    o.age = age;
    o.getName = function() {
        return this.name;
    }

    return o;
}

// 创建两个实例
var perTom = createPerson('TOM', 20);
var PerJake = createPerson('Jake', 22);

// 检验实例类型
console.log(perTom instanceof Object);  // true
console.log(PerJake instanceof Object);  // true
```

代码简单了很多，但仍有2个问题：

- 因为

  ```javascript
  // 声明一个中间对象，该对象就是工厂模式的模子
      var o = new Object();
  ```

  在模板内部，所以我们没法识别实例对象的类型。

  解决方案：用instanceof。

  ```javascript
  var obj = {};
  var foo = function() {}
  
  console.log(obj instanceof Object);  // true
  console.log(foo instanceof Function); // true
  ```

  因此，在工厂模式的基础上，我们需要升级为使用构造函数。

- 即使实例中的某些属性完全相同，但js仍然要为每个属性分配单独的内存空间。

  解决方案：使用原型存储这些不变的属性。

------

#### 5. 构造函数



##### 5.1. new关键字

构造函数其实就是工厂模式的一种具体实现，但又有其独特之处。

比如new关键字，就让构造函数变得与众不同。

举个栗子↓

```javascript
function demo() {
    console.log(this);
}

demo();  // window
new demo();  // demo
```

显然，使用new之后，函数内部发生了一些变化，this的指向改变了。

```javascript
通过new操作符调用构造函数，会经历以下4个阶段。

- 创建一个新的对象；
- 将构造函数的this指向这个新对象；
- 指向构造函数的代码，为这个对象添加属性，方法等；
- 返回新对象。

```



------

从4个阶段的角度出发，让我们来实现一个new关键字的功能函数↓



```javascript
// 先一本正经的创建一个构造函数，其实该函数与普通函数并无区别
var Person = function(name, age) {
    this.name = name;
    this.age = age;
    this.getName = function() {
        return this.name;
    }
}


// 将构造函数以参数形式传入
function New(func) {

    // 声明一个中间对象，该对象为最终返回的实例
    var res = {};
    if (func.prototype !== null) {

        // 将实例的原型指向构造函数的原型
        res.__proto__ = func.prototype;
    }

    // ret为构造函数执行的结果，这里通过apply，将构造函数内部的this指向修改为指向res，即为实例对象
    var ret = func.apply(res, [].slice.call(arguments, 1)); // 传入['tom', 20]这个数组作为参数
		// 总之，上面这行代码的意思就是：
  	// 执行func，把func的this指向res，并给其传参数为['tom', 20]，
  	// 将res赋值给ret，
  	// 所以res和ret就都变成了
    // {
    //   name: 'tom',
    //   age: 20
    //   getNmae () {
    //    return this.name
    //   }
    // }
  
  
    // 当我们在构造函数中明确指定了返回对象时，那么new的执行结果就是该返回对象
    if ((typeof ret === "object" || typeof ret === "function") && ret !== null) {
        return ret;
    }

    // 如果没有明确指定返回对象，则默认返回res，这个res就是实例对象
    return res;
}


// 通过new声明创建实例，这里的p1，实际接收的正是new中返回的res
var p1 = New(Person, 'tom', 20);
console.log(p1.getName());

// 当然，这里也可以判断出实例的类型了
console.log(p1 instanceof Person); // true
```



以上代码实现了New函数，功能与new关键字一样。

js底层再将  **`New(Person, 'tom', 20)`**   等效于  **`new Person( 'tom', 20)`**，就完成了new关键字的实现。





------

### 三. 原型

#### 1. 概念

我们创建的每一个函数，都有一个prototype属性，该属性指向一个对象。这个对象就是原型。

当我们创建模板函数时，可以根据需要将一些公共函数或公共属性通过prototype挂载到原型对象上。

原型对象中的constructor属性又指回模板函数。



而我们创建的每一个实例，都有一个-proto-属性，该属性指向模板函数的prototype属性。





------

我们举个例子来说明构造函数，实例与原型三者之间的关系 ↓

```javascript
// 声明构造函数
function Person(name, age) {
    this.name = name;
    this.age = age;
}

// 通过prototye属性，将方法挂载到原型对象上
Person.prototype.getName = function() {
    return this.name;
}

var p1 = new Person('tim', 10);
var p2 = new Person('jak', 22);
console.log(p1.getName === p2.getName); // true
```

*注意：每个函数都可以是构造函数，每个对象都可以是原型对象 。



------

#### 2. 属性/方法的优先级

我们现在知道，构建函数的prototype属性指向的原型对象用来存储公共方法和属性。

那么如果说当一个构造函数的原型中和实例中有着同一个方法，实例会优先访问哪里的方法呢？答案是实例中的方法。

这也是后面要说的原型链的规则之一。其实很简单，就是现在自己身上寻找某个属性/方法，找不到的话就去原型链上找，一直找，找到为止。

栗子↓

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.getName = function() {
        console.log('this is constructor.');
    }
}

Person.prototype.getName = function() {
    return this.name;
}

var p1 = new Person('tim', 10);

p1.getName(); // this is constructor.
```



------

#### 3. in

另外，我们可以用 `in` 方法来判断一个实例对象中有没有某个属性/方法。无论该属性/方法存在于对象中还是其-proto-中。

栗子↓

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype.getName = function() {
    return this.name;
}

var p1 = new Person('tim', 10);

console.log('name' in p1); // true
console.log('getName' in p1); // true
```

`in`  有一个重要的应用场景，就是判断当前设备是否是移动端。

很多人喜欢使用UA来判断，其实比较复杂。当然其也有 `in` 做不到的地方。

单纯判断是否是移动端的话，这样写即可↓

```javascript
isMobile = 'ontouchstart' in document;
```



------

#### 4. 更简单的原型写法

上面的例子中，我们是这么添加方法的：

```javascript
function Person() {}

Person.prototype.getName = function() {}
Person.prototype.getAge = function() {}
Person.prototype.sayHello = function() {}
... ...
```

其实可以这样写：

```javascript
function Person() {}

Person.prototype = {
    constructor: Person,
    getName: function() {},
    getAge: function() {},
    sayHello: function() {}
}
```

唯一要说的一点是：为什么prototype中要加一个 `constructor` 属性？

其实这样写相当于是改变了prototype的引用地址 → 到一个新的对象。这个新的对象与原型对象相互独立。

原型对象中是要有 `constructor ` 属性的，指向构建函数。

所以这种写法请务必加上 `constructor` 属性。



------

### 四. 原型链

原型对象也是一个对象，不要以为他有多高大上。所有的对象都可以是原型对象，或实例对象，甚至**即是原型对象又是实例对象**。

如果一个对象**即是原型对象又是实例对象**，那么原型链就形成了。



我们举个例子：

所有的函数都有一个toString方法，这个方法来自哪里呢？没错，来自  `Object构造函数`  的原型对象。

调用toString时，是从实例通过原型链一直往上查找并获取到的。

```javascript
// 先随便声明一个函数
function add() {}
```

那么它的原型链是这样的↓

![原型链](G:\myRoadOfWeb\js\我的文章\imgs\原型链.png)





------

### 五. 继承

基于原型链，我们可以轻松地实现继承。

上面说过，构造函数和原型的特性分别解决了工厂模式的两大痛点。而继承也分为构造函数的继承和原型的继承。

#### 1. 一般继承

我们先定义一个Person构造函数为父级，同时定义一个子级cPerson函数。子级要继承父级↓

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype.getName = function() {
    return this.name;
}
```



- 构造函数的继承

  父级作为一个构造函数，子级函数去继承父级函数，即是去得到父级的构造功能。

  那么父级的构造功能是什么呢？其本质就是new关键字实现的一个复制过程。

  ```javascript
  // 构造函数的继承
  function cPerson(name, age, job) {
      Person.call(this, name, age)
      this.job = job;
  }
  ```

  

- 原型继承

  上一节说的原型链就适用于原型继承。方法就是**将子级的原型对象设置为父级的实例对象**↓

  ```javascript
  // 继承原型
  cPerson.prototype = new Person()
  
  // 添加更多方法
  cPerson.prototype.getLive = function() {}
  ```

  再来个直观的对比↓

  ```javascript
  function Person(name, age) {
      this.name = name;
      this.age = age;
  }
  
  Person.prototype.getName = function() {
      return this.name;
  }
  
  /*
  * 上面是父级构造函数，有个getName方法，下面是子级构造函数。
  * 若子级构造函数的实例能成功调用到getName函数，则继承成功。
  */
  
  function cPerson(name, age, job) {
      Person.call(this, name, age);
      this.job = job;
  }
  
  let rj1 = new cPerson('rj1', 10, 'it')
  console.log('挂载进原型链之前:', rj1.getName())  //报错
  
  cPerson.prototype = new Person() // 关键代码
  
  let rj2 = new cPerson('rj2', 10, 'it')
  console.log('挂载进原型链之后:', rj2.getName())  // 'rj2'
  ```

  

------

#### 2. 更好的原型继承

