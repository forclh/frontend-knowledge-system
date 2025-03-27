
# HTTP

超文本传输协议（Hyper Text Transfer Protocol，*HTTP*）是一个广泛运用于互联网的应用层协议。

99%的情况下，前端开发者接触的都是HTTP协议。

该协议规定了两个方面的内容：

- **传递消息的模式**
- **传递消息的格式**

## 传递消息的模式

![image-20230112110047746](http://mdrs.yuanjin.tech/img/202301121100790.png)

HTTP使用了一种极为简单的消息传递模式，「请求-响应」模式

发起请求的称之为客户端，接收请求并完成响应的称之为服务器。

「请求-响应」完成后，一次交互结束。

## 传递消息的格式

![image-20230113214343300](http://mdrs.yuanjin.tech/img/202301132143345.png)

HTTP的消息格式是一种纯文本的格式，文本分为三个部分：

```
请求行
请求头

请求体
```

**具体每一部分写什么内容，要看具体的服务要求**

## 试一试

有非常多的工具可以发送http请求，这里推荐一个非常直观的工具

1. 安装`vscode`插件`REST Clinet`

   ![image-20230112114542925](http://mdrs.yuanjin.tech/img/202301121145973.png)

2. 新建文件`xxx.http`

3. 编写请求文本

```http
   POST /api/user/login HTTP/1.1
   Host: study.duyiedu.com
   Content-Type: application/json
   
   {
     "loginId":"admin",
     "loginPwd":"123123"
   }
```
4. 发送请求

## 熟悉关键信息

### 请求方法

请求行中的第一个单词是请求方法

**在HTTP协议中，请求方法仅有语义的区别**，只是表达了这次请求的「愿望」。

> 关于请求方法的协议原文见 [HTTP/1.1规范RFC7231-Chapter4](https://www.rfc-editor.org/rfc/rfc7231.html)
> ![image-20230113190854930](http://mdrs.yuanjin.tech/img/202301131908971.png)

比如`GET`表达了客户端想要获取一些东西，`POST`表达了客户端想要提交一些东西

常见的请求方法有：

- `GET`：获取
- `POST`：提交
- `PUT`：修改
- `DELETE`：删除

**具体在开发中应该选择什么请求方法，一定是看服务方的要求**

通常情况下：

- 获取数据一般使用`GET`
- 提交数据一般使用`POST`
- 各种静态资源的获取，一般使用`GET`

---

#### 请求方法的本质

**请求方法是请求行中的第一个单词，它向服务器描述了客户端发出请求的动作类型。在 HTTP 协议中，不同的请求方法只是包含了不同的语义，但服务器和浏览器的一些约定俗成的行为造成了它们具体的区别**

![](http://mdrs.yuanjin.tech/img/20210914102745.png)

```jsx
fetch('<https://www.baidu.com>', {
  method: 'heiheihei', // 告诉百度，我这次请求是来嘿嘿嘿的
});
```

上面的请求中，我们使用了自定义方法`heiheihei`。虽然百度服务器无法理解这样的请求是在干什么，但这样的请求也是可以正常发送到百度服务器的。

在实践中，客户端和服务器慢慢的形成了一个共识，约定俗成的规定了一些常见的请求方法：

- GET，表示向服务器获取资源。业务数据在请求行中，无须请求体
- POST，表示向服务器提交信息，通常用于产生新的数据，比如注册。业务数据在请求体中
- PUT，表示希望修改服务器的数据，通常用于修改。业务数据在请求体中
- DELETE，表示希望删除服务器的数据。业务数据在请求行中，无须请求体。
- OPTIONS，发生在跨域的预检请求中，表示客户端向服务器申请跨域提交
- TRACE，回显服务器收到的请求，主要用于测试和诊断
- CONNECT，用于建立连接管道，通常在代理场景中使用，网页中很少用到

#### GET 和 POST 的区别

**由于浏览器和服务器约定俗称的规则**，造成了 GET 和 POST 请求在 web 中的区别：

1. 浏览器在发送 GET 请求时，不会附带请求体
2. GET 请求的传递信息量有限，适合传递少量数据；POST 请求的传递信息量是没有限制的，适合传输大量数据。
3. GET 请求只能传递 ASCII 数据，遇到非 ASCII 数据需要进行编码；POST 请求没有限制
4. 大部分 GET 请求传递的数据都附带在 path 参数中，能够通过分享地址完整的重现页面，但同时也暴露了数据，若有敏感数据传递，不应该使用 GET 请求，至少不应该放到 path 中
5. 刷新页面时，若当前的页面是通过 POST 请求得到的，则浏览器会提示用户是否重新提交。若是 GET 请求得到的页面则没有提示。
6. GET 请求的地址可以被保存为浏览器书签，POST 不可以

---

### 请求头 - `Host`

`Host`标注了`URL`地址中的`Domain + Port`

示例：

Host: study.duyiedu.com
### 请求头 - `Content-Type`

`Content-Type`标注了附带的请求体是什么格式


文件传输：
```
Content-Type: multipart/form-data; boundary=aaa

--aaa
Content-Disposition: form-data; name="loginId"

admin
--aaa
Content-Disposition: form-data; name="loginPwd"

123456
--aaa
Content-Disposition: form-data; name="avatar"; filename="small.jpg"
Content-Type: image/jpeg

文件的二进制
--aaa--
```

| **Content-Type 值**                  | **描述**                                                     | **请求体格式**                                    | **示例**                                                                    |
| ----------------------------------- | ---------------------------------------------------------- | -------------------------------------------- | ------------------------------------------------------------------------- |
| `application/json`                  | 指定请求体是 JSON 格式的数据                                          | JSON 格式                                      | `{"name": "Alice", "age": 25}`                                            |
| `application/x-www-form-urlencoded` | 表示数据已被编码为键值对（key-value），与 URL 查询字符串类似                      | 键值对格式（`key1=value1&key2=value2`）             | `name=Alice&age=25`                                                       |
| `multipart/form-data`               | 用于上传文件或包含多部分数据（如文件、文本字段）                                   | 分段的多部分数据，每部分都有分隔符和 `Content-Disposition` 来标识 | 用于文件上传时，通常包含二进制文件和表单字段                                                    |
| `text/plain`                        | 指定请求体是纯文本格式                                                | 纯文本                                          | `Hello, this is plain text.`                                              |
| `application/xml`                   | 指定请求体是 XML 格式的数据                                           | XML 格式                                       | `<person><name>Alice</name><age>25</age></person>`                        |
| `application/javascript`            | 用于发送 JavaScript 代码（不常用）                                    | JavaScript 代码                                | `function sayHello() { console.log('Hello World!'); }`                    |
| `application/octet-stream`          | 表示任意二进制数据流，通常用于文件传输或下载                                     | 二进制数据                                        | 二进制文件内容，例如 `.png` 或 `.exe` 文件                                             |
| `text/html`                         | 表示请求体是 HTML 文档                                             | HTML 文本                                      | `<html><body><h1>Hello, HTML!</h1></body></html>`                         |
| `text/css`                          | 表示请求体是 CSS 样式表（不常用）                                        | CSS 样式表                                      | `body { background-color: #fff; }`                                        |
| `text/javascript`                   | 早期表示 JavaScript 的 MIME 类型，现在已被 `application/javascript` 取代 | JavaScript 代码                                | `console.log('Hello, JavaScript!');`                                      |
| `image/png`                         | 请求体是 PNG 格式的图片数据                                           | PNG 图片的二进制数据                                 | 二进制文件内容，例如 `.png` 图片                                                      |
| `image/jpeg`                        | 请求体是 JPEG 格式的图片数据                                          | JPEG 图片的二进制数据                                | 二进制文件内容，例如 `.jpeg` 或 `.jpg` 图片                                            |
| `image/gif`                         | 请求体是 GIF 格式的图片数据                                           | GIF 图片的二进制数据                                 | 二进制文件内容，例如 `.gif` 图片                                                      |
| `application/pdf`                   | 请求体是 PDF 文档                                                | PDF 文件的二进制数据                                 | 二进制文件内容，例如 `.pdf` 文件                                                      |
| `application/vnd.api+json`          | JSON 数据类型的特定子类型，通常用于 RESTful API                           | JSON 格式                                      | `{"data": {"type": "users", "id": "1", "attributes": {"name": "Alice"}}}` |

### 响应码

响应码（状态码、消息码）是响应行中的一个数字，后面往往跟上一个对应的单词，用于表达服务器对这个响应的整体「态度」

常见的响应码有：

![image-20230112131000507](http://mdrs.yuanjin.tech/img/202301121310570.png)


常见的状态码有：

1. 200 OK：一切正常。

2. 301 Moved Permanently：资源已被永久重定向。

   ```你的请求我收到了，但是呢，你要的东西不在这个地址了，我已经永远的把它移动到了一个新的地址，麻烦你取请求新的地址，地址我放到了响应头的Location中了```

   > 试试请求：www.douyutv.com

3. 302 Found：资源已被临时重定向。

   ```你的请求我收到了，但是呢，你要的东西不在这个地址了，我临时的把它移动到了一个新的地址，麻烦你取请求新的地址，地址我放到了请求头的Location中了```

4. 304 Not Modified：文档内容未被修改。

   ```你的请求我收到了，你要的东西跟之前是一样的，没有任何的变化，所以我就不给你结果了，你自己就用以前的吧。啥？你没有缓存以前的内容，关我啥事```

5. 400 Bad Request：语义有误，当前请求无法被服务器理解。

   ```你给我发的是个啥啊，我听都听不懂```

6. 403 Forbidden：服务器拒绝执行。

   ```你的请求我已收到，但是我就是不给你东西```

7. 404 Not Found：资源不存在。

   ```你的请求我收到了，但我没有你要的东西```

8. 500 Internal Server Error：服务器内部错误。

   ```你的请求我已收到，但这道题我不会，解不出来，先睡了```

### 响应头 - `Content-Type`

`Content-Type`标注了附带的响应体是什么格式


