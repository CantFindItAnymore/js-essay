### 一. Set

类似于数组，但是成员的值都是不重复的。

```javascript
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4


```

- new Set()定义。
- new Set().add(x)添加数据。
- new Set([])接收数组等作为参数。
- Set结构的遍历
  - `keys()`：返回键名的遍历器
  - `values()`：返回键值的遍历器
  - `entries()`：返回键值对的遍历器
  - `forEach()`：使用回调函数遍历每个成员

### 二. Map

类似于对象，但是它的键不再局限于字符串，什么都可以用来做键。

```javascript
const m = new Map();
const o = {p: 'Hello World'};

m.set(o, 'content') // set方法返回的是当前的Map对象，所以可以链式操作
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false

// 将数组作为参数
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```