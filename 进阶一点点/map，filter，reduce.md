### 一. map

```javascript
Array.map((item, index, array) => {})
```

更新原数组的同时保留原始值，不会删除任何东西。根据原数组的元素逐个生成新的元素，并合成新的数组。



### 二. filter

filter：过滤。

对原数组中的每个元素检查过滤一遍，将符合规则的元素放到一个新数组中。

```javascript
Array.filter((item, index, array) => {})
```



### 三. reduce

将结果汇总成单个返回值。

```javascript
Array.reduce(Acc, item, index, array)

// Acc：累加器
// item：当前值
// index：当前下标
// array：原数组
```

依次对原数组中的元素做一些处理后，将结果传给累加器。最后由累加器返回单个返回值。

eg↓

```javascript
const array1 = [1, 2, 3, 4];
const reducer = (accumulator, currentValue) => accumulator + currentValue;

// 1 + 2 + 3 + 4
console.log(array1.reduce(reducer));
// expected output: 10

// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(reducer, 5)); // 注意：5指累加器的初始值
// expected output: 15
```

应用场景

```javascript
const array = [ 1, 2, 3, 4];
// 连加
console.log(array.reduce((p, c) => p + c)); // => 10
// 连乘
console.log(array.reduce((p, c) => p * c)); // => 24
```

