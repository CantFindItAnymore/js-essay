

### 一. MVC

MVC模式的意思是，软件可以分成三个部分:

- 视图（View）：用户界面
- 控制器（Controller）：业务逻辑
- 模型（Model）：数据保存

各部分之间的通信方式如下:

v的改变，都是c去**主动**触发的。

同时c还要**主动**去获取m和修改m。



通信流：

  m →← c → v



------

### 二. MVP

MVP 模式将 Controller 改名为 Presenter，同时改变了通信方向。 

- 各部分之间的通信，都是双向的。
- View 与 Model 不发生联系，都通过 Presenter 传递。
- View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里。

![d](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015020109.png)

v的改动，会**主动**告诉p，p接收到后会与m进行通信。



------

### 三. MVVM

MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。 

唯一的区别是，它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。

![r](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015020110.png)

可以理解为mvp的升级版：

v变化了，直接主动告诉m层（通过架设好的桥梁ViewModel），而不经过p层。