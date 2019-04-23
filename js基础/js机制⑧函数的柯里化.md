### 一. 高阶函数

高阶函数的采用使得JavaScript适合用来做函数式编程。 

**概念**：高阶函数是对其他函数进行操作的函数，它可以

- 将函数作为参数。
- 返回函数。



------

先举个栗子，我们现在需要把一个数组的所有元素都*2返回回来。

```javascript
// 不使用高阶函数
var a = [1, 2, 3]
function fly (arr) {
  var b = []
  for (item of arr) {
    console.log(item)
    b.push(item*2)
  }
  return b
}

console.log(fly(a))
```

```javascript
// 使用高阶函数
var a = [1, 2, 3]
var b = a.map(item => item * 2)
console.log(b)
```

从两个例子可以看出，高阶函数在简化代码上起了很大的作用。虽然使用了ES6，但是其依然借助了高阶函数。

同时，这是一种函数式编程的思维。便于维护和自动化测试。



### 二. 创建自己的高阶函数

如果第一点没有太大说服力，那么我们来自己写一个高阶函数，实现map功能。

和上个栗子一样，我们现在需要把一个数组的所有元素都*2返回回来。

```javascript
var a = [1, 2, 3]
function fly (arr, fn) {
  var b = []
  for (item of arr) {
    b.push(fn(item))
  }
  return b
}

fly(a, function(item) {
  return item * 2
})
```

此时，fly就和map的功能基本一致了。并且封装好后可以轻松应用到其它场景。这就是函数式编程的好处之一。





------

### 三. 函数的柯里化

函数的柯里化其实是高阶函数的一种特殊用法。

**概念：**柯里化是指这样一个函数(假设叫做createCurry)，他接收函数A作为参数，运行后能够返回一个新的函数。并且这个新的函数能够处理函数A的剩余参数。

举个栗子↓

```javascript
function A(a, b, c) {
    // do something
}

var _A = createCurry(A);

_A(1, 2, 3);
_A(1, 2)(3);
_A(1)(2, 3);
_A(1)(2)(3);
_A(1, 2, 3);
// 以上5个表达式都是等价的
```

函数A被createCurry转化之后得到柯里化函数-A，-A能够处理A的所有剩余参数。因此柯里化也被称为 `部分求值`。 



------

在简单的场景下，我们不需要借助柯里化通用式来转化得到柯里化函数。可以自己写。

例子↓

```javascript
function add(a, b, c) {
    return a + b + c;
}

  function _add(a) {
      return function(b) {
          return function(c) {
              return a + b + c;
          }
      }
  }

add(1, 2, 3);
_add(1)(2)(3);
// 以上2个表达式等价，_add参数只能这么写
```

这样简单实现了一个柯里化函数，但是这是远远不够的。继续优化↓

从_add的角度出发，我们知道其实函数的柯里化就是一个收集参数的过程，然后在内部处理好。

按照这个思路，再封装一下↓

```javascript
// 简单实现，参数只能从右到左传递
function createCurry(func, args) {

    var arity = func.length;
    var args = args || [];

    return function() {
        var _args = [].slice.call(arguments);
        [].push.apply(_args, args);

        // 如果参数个数小于最初的func.length，则递归调用，继续收集参数
        if (_args.length < arity) {
            return createCurry.call(this, func, _args);
        }

        // 参数收集完毕，则执行func
        return func.apply(this, _args);
    }
}
```

好难，晕了。先放放。。。