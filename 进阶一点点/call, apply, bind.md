**bind()是将某个函数绑定到某个对象上。**

bind第一个参数是指定的对象，后续参数都是传入方法的值。需要注意的是bind函数不会使得被改变的函数立即执行。而call和apply会立即执行。



**call(), apply()可看做某个对象的方法。**

call和apply的第一个参数都是指定的对象，后续参数有所区别：

- call	除了第一个参数，后续的所有参数都是传入方法的值。
- apply 除了第一个参数，第二个参数是一个数组，就是传入方法的值。

eg↓

```js
var obj = {};
 
function foo(a, b, c) {
  console.log(b);
}
 
foo.call(obj, 1, 2, 3) // 打印结果： 2;
foo.apply(obj, [1, 2, 3]) // 打印结果： 2;
```

