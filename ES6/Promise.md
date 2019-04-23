下面的题目可以测试你promise的掌握程度。

1. 

```javascript
const promise = new Promise((resolve, reject) => {
  console.log(1)
  resolve()
  console.log(2)
})
promise.then(() => {
  console.log(3)
})
console.log(4)
```

- 结果：1,2,4,3。
- 知识点：
  - promise构造函数是同步的，而then，catch是异步的。



2. 

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('success')
  }, 1000)
})
const promise2 = promise1.then(() => {
  // reject('err') // 这种写法会报错，因为promise的状态已经变为了resolved。
  throw new Error('err') // 此处返回一个新的promise
})

setTimeout(() => {
  console.log('promise21', promise1)
  console.log('promise22', promise2)
}, 2000)

console.log('promise11', promise1)
console.log('promise12', promise2)
```

- 结果：

  promise11 {<pending>}
  promise12 {<pending>}
  promise21 {<resolved>: 'success'}
  promise22 {<rejected>: 'err'}

- 知识点：

  - promise状态只能是pengding→resolved或pending→rejected。
  - setTimeout和then，catch都是异步的。



3. 

```javascript
const promise = new Promise((resolve, reject) => {
  resolve('success1')
  reject('error')
  resolve('success2')
})

promise
  .then((res) => {
    console.log('then: ', res)
  })
  .catch((err) => {
    console.log('catch: ', err)
  })
```

- 结果：then: success1
- 知识点：构造函数中的 resolve 或 reject 只有第一次执行有效，多次调用没有任何作用，呼应代码二结论：promise 状态一旦改变则不能再变。 



4. 

```javascript
Promise.resolve(1)
  .then((res) => {
    console.log(res)
    return 2
  })
  .catch((err) => {
    return 3
  })
  .then((res) => {
    console.log(res)
  })
```

- 结果：1, 2
- 知识点：promise可以链式调用，then和catch返回一个新的promise。



5. 

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('once')
    resolve('success')
  }, 1000)
})

const start = Date.now()
promise.then((res) => {
  console.log(res, Date.now() - start)
})
promise.then((res) => {
  console.log(res, Date.now() - start)
})
```

- 结果：

  once

  success 1001

  success 1002

- 知识点：promise 的 .then 或者 .catch 可以被调用多次，但这里 Promise 构造函数只执行一次。或者说 promise 内部状态一经改变，并且有了一个值，那么后续每次调用 .then 或者 .catch 都会直接拿到该值。 



6. 

```javascript
Promise.resolve()
  .then(() => {
  	// return new Error('error!!!')
    // throw new Error('error!!!')
  	return Promise.reject(new Error('error!!!'))
  })
  .then((res) => {
    console.log('then: ', res)
  })
  .catch((err) => {
    console.log('catch: ', err)
  })
```

- 知识点：promise 的 reject有两种方式：
  - throw new Error('error!!!')
  - return Promise.reject(new Error('error!!!'))

如果使用return new Error('error!!!')，它也会被包裹成一个promise对象，但不会被catch接收，而会被下一个then接收。

7. node执行环境

```javascript
process.nextTick(() => {
  console.log('nextTick')
})
Promise.resolve()
  .then(() => {
    console.log('then')
  })
setImmediate(() => {
  console.log('setImmediate')
})
console.log('end')
```

- 结果：

  end

  nextTick

  then

  setImmediate

- 知识点：process.nextTick 和 promise.then 都属于 microtask，而 setImmediate 属于 macrotask，在事件循环的 check 阶段执行。事件循环的每个阶段（macrotask）之间都会执行 microtask，事件循环的开始会先执行一次 microtask。 

