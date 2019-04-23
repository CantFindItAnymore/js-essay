- DNS 解析:将域名解析成 IP 地址
- TCP 连接：TCP 三次握手
- 建立TCP连接后发送起HTTP 请求
- 服务器处理请求并返回 HTTP 报文
- 浏览器解析渲染页面
- 断开连接：TCP 四次挥手



### 一. 什么是URL

统一资源定位符，用于定位互联网上资源，俗称网址。 

```javascript

scheme: // host.domain:port / path / filename ? query = 123 # fragment

scheme - 定义因特网服务的类型。常见的协议有 http、https、ftp、file，
         其中最常见的类型是 http，而 https 则是进行加密的网络传输。
host - 定义域主机（http 的默认主机是 www）
domain - 定义因特网域名，比如 baidu.com
port - 定义主机上的端口号（http 的默认端口号是 80）
path - 定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）。
filename - 定义文档/资源的名称
query - 即查询参数
fragment - 即 # 后的hash值，一般用来定位到某个位置
```



### 二.DNS域名解析

在浏览器输入网址后，首先要经过域名解析，因为浏览器并不能直接通过域名找到对应的服务器，而是要通过 IP 地址。 

1.  IP地址

   IP 地址是 IP 协议提供的一种统一的地址格式，它为互联网上的每一个网络和每一台主机分配一个逻辑地址，以此来屏蔽物理地址的差异。 

2. 什么是域名解析

    DNS 协议提供通过域名查找 IP 地址，或逆向从 IP 地址反查域名的服务。 DNS是一个服务器。

3. 浏览器如何通过域名去查询 URL 对应的 IP 呢？ 

   ![dns域名解析](https://user-gold-cdn.xitu.io/2019/2/27/1692e385403c39ab?imageslim)

4. 1

5. 1

6. 1

7. 1

   

###一. 

###一. 

###一. 

###一. 

###一. 

###一. 

