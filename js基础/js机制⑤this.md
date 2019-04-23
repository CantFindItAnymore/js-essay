### 广为流传的错误结论

谁调用this，this就指向谁。 

不准确，误导人。

### this的确定时间

函数被调用，上下文入栈，在上下文创建阶段会创建变量对象，建立作用域链，确定this指向。

所以**this是在函数被调用时被确定的**。

注意this确定后就不会变了。

### 全局对象中的this

浏览器中，全局对象是window，

window的变量对象比较特殊，就是window对象本身。

window的this也比较特殊，就指向window对象本身。

```javascript
// 通过this绑定到全局对象
this.a2 = 20;
// 通过声明绑定到变量对象，但在全局环境中，变量对象就是它自身
var a1 = 10;
// 仅仅只有赋值操作，标识符会隐式绑定到全局对象
a3 = 30;
// 输出结果会全部符合预期
console.log(a1);
console.log(a2);
console.log(a3);
```

------

### 函数中的this

函数中的this由 `调用方式` 决定。

如果调用该函数的是一个对象，则this指向该对象。

如果没有谁调用该函数，该函数独立执行。则this指向undefined。（非严格模式下this的指向又会从undefined变为全局对象。）

```javascript
// demo01
var a1 = 20
function fn1() {
    console.log(this.a1)
}
fn1() // 20

// demo02
var a2 = 20;
function fn2() {
    function foo2() {
        console.log(this.a2)
    }
    foo2()
}
fn2() // 20

// demo03
var a3 = 20
var obj3 = {
    a3: 10,
    c3: this.a3 + 20, // 这里的this指向window，因为对象的{}并不会形成作用域。
    fn3: function () {
        return this.a3
    }
}

console.log(obj3.c3) // 40
console.log(obj3.fn3()) // 10
```

------

### 使用call，apply，bind显示指定this

所有的函数都有这俩方法，用于手动设置this的指向并执行该函数。

- 他们第一个参数都为 `this将要指向的对象` 。

```javascript
function fn() {
    console.log(this.a)
}
var obj = {
    a: 20
}

fn.call(obj) // 20
```

- 他们的第二个可选参数为/传给将要执行函数/的参数。

call方法一个一个传递，apply以数组的形式传递，这是他们唯一的区别。并且他们会自动执行函数。bind也是一个一个传递，可是不会自动执行函数。

```javascript
function fn(num1, num2) {
    console.log(this.a + num1 + num2)
}
var obj = {
    a: 20
}

fn.call(obj, 100, 10) // 130
fn.apply(obj, [20, 10]) // 50
```

------

应用场景：

- 将类数组对象转换为数组。

  ```javascript
  function exam(a, b, c, d, e) {
  
      // 先看看函数的自带属性 arguments 什么是样子的
      console.log(arguments)
  
      // 使用call/apply将arguments转换为数组, 返回结果为数组，arguments自身不会改变
      var arg = [].slice.call(arguments)
  
      console.log(arg)
  }
  
  exam(2, 8, 9, 10, 3)
  
  // result:
  // { '0': 2, '1': 8, '2': 9, '3': 10, '4': 3 }
  // [ 2, 8, 9, 10, 3 ]
  //
  // 也常常使用该方法将DOM中的nodelist转换为数组
  // [].slice.call( document.getElementsByTagName('li') )
  ```

  妙啊~:clinking_glasses:

  ------

  

- 实现继承。

  ```javascript
  // 定义父级的构造函数
  var Person = function(name, age) {
      this.name = name;
      this.age  = age;
      this.gender = ['man', 'woman'];
  }
  
  // 定义子类的构造函数
  var Student = function(name, age, high) {
  
      // use call
      Person.call(this, name, age);
      this.high = high;
  }
  Student.prototype.message = function() {
      console.log('name:'+this.name+', age:'+this.age+', high:'+this.high+', 							gender:'+this.gender[0]+';');
  }
  
  new Student('xiaom', 12, '150cm').message();
  
  // result
  // ----------
  // name:xiaom, age:12, high:150cm, gender:man;
  ```

  妙啊~:clinking_glasses:

  ------

  

- 闭包时，保证this指向不变。

  ```javascript
  var obj = {
      a: 20,
      getA: function() {
          setTimeout(function() {
              console.log(this.a) // 此处的this指向全局
          }, 1000)
      }
  }
  
  obj.getA()
  ```

  - 解决方案1：

  ```javascript
  var obj = {
      a: 20,
      getA: function() {
          var self = this // 这里的this指向调用对象或window（独立执行时）
          setTimeout(function() {
              console.log(self.a)
          }, 1000)
      }
  }
  
  obj.getA()
  ```

  - 解决方案2：

  ```javascript
  function bind(fn, obj) {
      return function() {
          return fn.apply(obj)
      }
  }
  
  var obj = {
      a: 20,
      getA: function() {
          setTimeout(
            bind(function() {
              console.log(this.a) // 这里的this指向等于下一行代码的this指向
          	}, this), // 这里的this是getA中的this，指向obj。也可以直接写obj
            1000
          )
      }
  }
  
  obj.getA()
  ```



------

### 构造函数和原型方法上的this

指向实例。

```javascript
function Person(name, age) {

    // 这里的this指向了谁?
    this.name = name
    this.age = age 
}

Person.prototype.getName = function() {

    // 这里的this又指向了谁？
    return this.name
}

// 上面的2个this，是同一个吗，他们是否指向了原型对象？

var p1 = new Person('Nick', 20)
p1.getName()
```



通过new操作符调用构造函数，会经历以下4个阶段。

- 创建一个新的对象；
- 将构造函数的this指向这个新对象；
- 指向构造函数的代码，为这个对象添加属性，方法等；
- 返回新对象。

------

至于在原型上/添加的方法/里的this，很简单。因为调用方式不是独立的，所以this指向调用方，即p1。

