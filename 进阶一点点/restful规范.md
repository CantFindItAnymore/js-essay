[RESTful](http://www.ruanyifeng.com/blog/2011/09/restful.html) 是目前最流行的 API 设计规范，用于 Web 数据接口的设计。 



### 一. URL设计

RESTful 的核心思想就是，客户端发出的数据操作指令都是"动词 + 宾语"的结构。比如，`GET /articles`这个命令，`GET`是动词，`/articles`是宾语。 

------

1. 动词通常就是五种 HTTP 方法，对应 CRUD 操作。 

   - GET：读取（Read）
   - POST：新建（Create）
   - PUT：更新（Update）
   - PATCH：更新（Update），通常是部分更新
   - DELETE：删除（Delete）

   根据 HTTP 规范，动词一律大写。 

------

2. 宾语必须是名词，并且建议使用复数。因为宾语是被动词操作的对象，并且一般都是资源的合集。

3. 避免多级URL

   常见的情况是，资源需要多级分类，因此很容易写出多级的 URL，比如获取某个作者的某一类文章。 

   ```javascript
   GET /authors/12/categories/2
   ```

   这种 URL 不利于扩展，语义也不明确，往往要想一会，才能明白含义。 

   更好的做法是，除了第一级，其他级别都用查询字符串表达。 

   ```javascript
   GET /authors/12?categories=2
   ```

4. 1

5. 1

### 二. 状态码

1. 状态码必须精确

   客户端的每一次请求，服务器都必须给出回应。回应包括 HTTP 状态码和数据两部分。

   HTTP 状态码就是一个三位数，分成五个类别:

   - `1xx`：相关信息 (API 不需要`1xx`状态码)
   - `2xx`：操作成功
   - `3xx`：重定向
   - `4xx`：客户端错误
   - `5xx`：服务器错误

### 三. 服务器回应

1. 不要返回纯本文

   API 返回的数据格式，不应该是纯文本，而应该是一个 JSON 对象，因为这样才能返回标准的结构化数据。所以，服务器回应的 HTTP 头的`Content-Type`属性要设为`application/json`。 

2. 发生错误时，不要返回 200 状态码

   正确的做法是，状态码反映发生的错误，具体的错误信息放在数据体里面返回。下面是一个例子。 

   ```
   HTTP/1.1 400 Bad Request
   Content-Type: application/json
   
   {
     "error": "Invalid payoad.",
     "detail": {
        "surname": "This field is required."
     }
   }
   ```

3. 提供链接

   API 的使用者未必知道，URL 是怎么设计的。一个解决方法就是，在回应中，给出相关链接，便于下一步操作。这样的话，用户只要记住一个 URL，就可以发现其他的 URL。这种方法叫做 HATEOAS。 

   举例来说，GitHub 的 API 都在 [api.github.com](https://api.github.com/) 这个域名。访问它，就可以得到其他 URL。 