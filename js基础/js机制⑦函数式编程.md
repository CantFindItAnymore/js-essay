### 一. 函数声明、函数表达式、匿名函数与自执行函数

js中，定义一个函数有3种方式：

① 声明式

```javascript
function a () {
  console.log(1)
}
a()
```

② 表达式

```javascript
var a = function () {
  console.log(1)
}
a()
```

③ 构造器

```javascript
var a = new Function('console.log(2)')
a()
```



------

从创建变量对象的角度来看，后两种实质上属于变量声明，第一种属于函数声明。



------

#### `函数声明`：

创建上下文时，

- 创建阶段

在创建变量对象时，

第2步为寻找`函数声明`（注意不是变量声明的函数，是函数声明的函数）：属性为函数名，属性值为函数地址。

- 执行阶段

上下文中的代码才开始执行。

→所以无论函数声明在何处，只要在一个上下文中，我们在任何位置都可以访问到它。

```javascript
console.log(fn) //可以打印出来

function fn () {
  return 1
}
```



------

#### `函数表达式`

创建上下文时，

- 创建阶段

在创建变量对象时，

第2步为寻找`函数声明`，可是函数表达式不是函数声明。

第3步为寻找变量声明，值为undefined。

- 执行阶段

上下文中的代码才开始执行。

→所以函数表达式的位置必须在调用它的代码之前，否则是undefined。

```javascript
console.log(a) //undefined

var a = function fn () {
  return 1
}
```



------

#### `匿名函数`

匿名函数，就是指的没有被显示进行赋值操作的函数。它的使用场景，多作为一个参数传入另一个函数中。 

```javascript
var a = 10;
var fn = function(bar, num) {
    return bar() + num;
}

fn(function() {
    return a;
}, 20)
```

上例中，fn的第一个参数传入了一个匿名函数。虽然该匿名函数没有显示的进行赋值操作，我们没有办法在外部执行上下文中引用到它，但是在fn函数内部，我们将该匿名函数赋值给了变量bar，保存在了fn变量对象的arguments对象中。 

分析一波：

- fn函数执行↓

- 上下文创建阶段

  ```javascript
  VO(fn) = {
      arguments: {
          bar: undefined,
          num: undefined,
          length: 2
      }
  }
  ```

  

- 上下文执行阶段

  **VO→AO**

  ```javascript
  AO(fn) = {
      arguments: {
          bar: function() { return a },
          num: 20,
          length: 2
      }
  }
  ```

  



------

####  `函数自执行与块级作用域` 

在ES5时代，没有块级作用域。有时候为了实现闭包，人们就用自执行函数来模拟一个作用域。

举个之前用过的例子↓

```javascript
for (var i=1; i<=5; i++) {
  setTimeout(
   	function () {
      console.log(i) // 666666
    },
    i*1000
 	)
}
```

```javascript
// 包裹一层自执行函数，形成闭包。
for (var i=1; i<=5; i++) {
  (function (ii) {
    setTimeout(
      function () {
        console.log(ii) // 12345
      },
      ii*1000
    )
  })(i)
}
```

函数自执行，就是匿名函数的一个具体应用。



------

自执行函数的另一大应用就是和闭包一起完成`模块化`：

一个模块包含：

- 私有变量
- 私有方法
- 公有变量
- 共有方法



私有变量和私有方法比较好理解，函数外部本来就不能访问内部的变量对象。

那么公有变量和共有方法呢？没错，结合闭包就可以让外部访问到。

举个栗子↓

```javascript
(function() {
    // 私有变量
    var age = 20
    var name = 'Tom'

    // 私有方法
    function getName() {
        return `my name is ` + name;
    }

    // 共有方法
    function getAge() {
        return  `my age is ` + age
    }

    // 将引用保存在外部执行环境的变量中，形成闭包，防止该执行环境被垃圾回收
    window.getAge = getAge
})()
```



------

以上的栗子很简单，我们来个牛逼点的模块化→状态管理器的实现原理，请看↓

```javascript
// 自执行创建模块
(function() {
    // states 结构预览
    // states = {
    //     a: 1,
    //     b: 2,
    //     m: 30,  
    //     o: {}
    // }
    var states = {};  // 私有变量，用来存储状态与数据

    // 判断数据类型
    function type(elem) {
        if(elem == null) {
            return elem + '';
        }
        return toString.call(elem).replace(/[\[\]]/g, '').split(' ')[1].toLowerCase();
    }


    /**
     * @Param name 属性名
     * @Description 通过属性名获取保存在states中的值
    */
    function get(name) {
        return states[name] ? states[name] : '';
    }

    function getStates() {
        return states;
    }

    /*
    * @param options {object} 键值对
    * @param target {object} 属性值为对象的属性，只在函数实现时递归中传入
    * @desc 通过传入键值对的方式修改state树，使用方式与小程序的data或者react中的setStates类似
    */
    function set(options, target) {
        var keys = Object.keys(options);
        var o = target ? target : states;

        keys.map(function(item) {
            if(typeof o[item] == 'undefined') {
                o[item] = options[item];
            }
            else {
                type(o[item]) == 'object' ? set(options[item], o[item]) : o[item] = options[item];
            }
            return item;
        })
    }

    // 对外提供接口
    window.get = get;
    window.set = set;
    window.getStates = getStates;
})()

// 具体使用如下

set({ a: 20 });     // 保存 属性a
set({ b: 100 });    // 保存属性b
set({ c: 10 });     // 保存属性c

// 保存属性o, 它的值为一个对象
set({
    o: {
        m: 10,
        n: 20
    }
})

// 修改对象o 的m值
set({
    o: {
        m: 1000
    }
})

// 给对象o中增加一个c属性
set({
    o: {
        c: 100
    }
})
console.log(getStates())
```

分析：





------

注意自执行函数还可以这么写：

- `!function () {} ()`
- `+function () {} ()` 



------

### 二. 函数参数传递方式都是`按值传递`，而不是按引用传递

我们之前说过`基本数据类型`和`引用数据类型`复制时的差异：

- 基本数据类型在复制时会变成两个独立的值，互不影响。
- 应用数据类型在复制时引用地址会变成两个独立的值，但是即使独立却依然都指向同一个地址。所以改变一个数据会影响到另一个。



------

当值作为函数的参数传递到函数内部时，也有着类似的差异。

当函数执行时，第一步创建上下文→创建变量对象→寻找参数放入arguments对象。

这个过程，其实就是对参数进行了一次复制操作。有点抽象，我们看个例子↓

```javascript
// 参数为基本数据类型
var a = 20

function fn(a) {
    a = a + 10
    return a
}
fn(a)
console.log(a) // 20


// 对比不传参数
var a = 20

function fn() {
    a = a + 10
}
fn(a)
console.log(a) // 30
```

```javascript
// 参数为引用数据类型（修改里面的值）
var a = { m: 10, n: 20 }
function fn(a) {
    a.m = 20
    return a
}

fn(a)
console.log(a)   // { m: 20, n: 20 }


// 参数为引用数据类型（修改整个值）
var a = { m: 10, n: 20 }
function fn(a) {
    a = {}
  	a.m = 44
}

fn(a)
console.log(a)   // { m: 10, n: 20 }
```

*注意

```javascript
a = {age: 12}

b = a

b = {}

console.log(a) // {age: 12}
console.log(b) // {}
```

像这样的情况的话，b的指针已经变了，不再和a指向一个地址了。而不是将 `{age: 12}` 变为 `{}`。



------

所以，我不能理解为什么函数的参数是值传递，当做引用传递来理解是没有问题的。

以高程为准，先记住是按值传递的吧。以后再回头来搞这块。



------

### 三. 函数式编程

函数式编程是一种编程形式，让你能将函数作为参数传递给其他函数并且能够将函数作为值返回。在函数式编程中，我们依据函数来思考和写代码。

JavaScript，Haskell，Clojure，Scala和Erlang等语言是能够用来做函数式编程的语言。



------

例如现在我们要把一个数组里的Number类型数据挑选出来，以下就是命令式的编程思维↓

```javascript
var array = [1, 3, 'h', 5, 'm', '4'];
var res = [];
for(var i = 0; i < array.length; i ++) {
    if (typeof array[i] === 'number') {
        res.push(array[i]);
    }
}
```

这种方式没有问题，可是不具有复用性。函数式编程的思维则要求我们把它封装起来以备调用↓

```javascript
function getNumbers(array) {
    var res = [];
    array.forEach(function(item) {
        if (typeof item === 'number') {
            res.push(item);
        }
    })
    return res;
}


// 以上是我们的封装，以下是功能实现
var array = [1, 3, 'h', 5, 'm', '4'];
var res = getNumbers(array);
```





函数式编程还有几个特征：

#### 1. 封装性，复用性

#### 2. 函数是第一等公民

是指函数在js中与其他数据类型一样，众生平等~可以赋值给变量，可以作为参数，可以传入另一个函数等等...

#### 3. 只使用表达式，不使用语句

函数式编程要求，只使用表达式，不使用语句。也就是说，每一步都是单纯的运算，而且都有返回值。

```javascript
var ele = document.querySelector('.test');
function setBackgroundColor(color) {
    ele.style.backgroundColor = color;
}

// 多处使用
setBackgroundColor('red');
setBackgroundColor('#ccc');
```

优化↓

```javascript
function setBackgroundColor(ele, color) {
    ele.style.backgroundColor = color;
    return color;
}

// 多处使用
var ele = document.querySelector('.test');
setBackgroundColor(ele, 'red');
setBackgroundColor(ele, '#ccc');
```



#### 4. 纯函数

相同的输入总会得到相同的输出，不会产生副作用。（幂等？）这就是纯函数。

所谓的副作用，比如修改了变量的值。

要求没有副作用则函数需要保持独立性，所有功能就是返回一个值，没有其它功能。

举个栗子，现在我们写一个返回数组最后一个值的函数↓

```javascript
function getLast(arr) {
    return arr[arr.length];
}

function getLast_(arr) {
    return arr.pop();
}

var source = [1, 2, 3, 4];

var last = getLast(source); // 返回结果4 原数组不变
var last_ = getLast_(source); // 返回结果4 原数据最后一项被删除
```

然而其实，在js原生方法中，有着很多不纯的函数。



#### 5. 闭包

说过了。

#### 6. 柯里化

下篇文章单独来摆。