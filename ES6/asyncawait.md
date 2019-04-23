### 一. 具体规则

- 凡是在前面加了async的函数在执行后都会返回一个Promise对象。
- await必须在async函数里使用。
- await后面需要跟promise对象，不然没有意义。而且await后面的promise不用写then，因为await已经获取到了。

### 二. 相对于promise

以下这种情况才建议使用async：

- 要执行多个异步任务。
- 并且这些个任务有前后依赖的关系(比如后者依赖前者的结果)。

