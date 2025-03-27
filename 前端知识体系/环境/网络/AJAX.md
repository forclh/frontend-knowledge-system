## AJAX

> 浏览器本身就具备网络通信的能力，但在早期，浏览器并没有把这个能力开放给JS。
>
> 最早是微软在IE浏览器中把这一能力向JS开放，让JS可以在代码中实现发送请求，并不会刷新页面，这项技术在2005年被正式命名为AJAX（**A**synchronous **J**avascript **A**nd **X**ML）

AJAX 就是指在web应用程序中异步向服务器发送请求。

它的实现方式有两种，`XMLHttpRequest 简称XHR`和`Fetch`

以下是两者的对比

| 功能点                   | XHR      | Fetch     |
| ------------------------ | -------- | --------- |
| 基本的请求能力           | ✅        | ✅         |
| 基本的获取响应能力       | ✅        | ✅         |
| 监控请求进度             | ✅        | ❌         |
| 监控响应进度             | ✅        | ✅         |
| Service Worker中是否可用 | ❌        | ✅         |
| 控制cookie的携带         | ❌        | ✅         |
| 控制重定向               | ❌        | ✅         |
| 请求取消                 | ✅        | ✅         |
| 自定义referrer           | ❌        | ✅         |
| 流                       | ❌        | ✅         |
| API风格                  | `Event`  | `Promise` |
| 活跃度                   | 停止更新 | 不断更新  |

## 实战

### 请求并获取响应数据

请求地址：https://study.duyiedu.com/api/herolist

请求方法：GET

响应类型：application/json

响应结果：王者荣耀英雄数据

### 上传文件并监控进度

#### 准备工作：启动本地文件上传服务器

**如果没有安装node**

1. 下载安装node，https://nodejs.org/zh-cn/

2. 打开命令行工具，设置淘宝源

   ```shell
   npm config set registry https://registry.npm.taobao.org
   ```

**安装依赖**

1. 在命令行进入`upload-server`目录
2. 运行`npm i`

**启动服务器**

1. 在命令行进入`upload-server`目录
2. 运行`npm start`

#### 上传接口

请求路径：`/upload/single`

请求方法：`POST`

消息格式：`multipart/form-data`

字段名称：`avatar`

允许的后缀名：`['.jpg', '.jpeg', '.bmp', '.webp', '.gif', '.png']`

最大尺寸：`1M`

响应格式：`JSON`

响应结果示例：

```json
// 成功
{
  "data": "文件的访问地址"
}
// 失败：后缀名不符号要求
{
  "errCode": 1,
  "errMsg": "后缀名不符合要求"
}
// 失败：文件过大
{
  "errCode": 2,
  "errMsg": "文件过大"
}
```

### 登录和鉴权

由于HTTP协议的特点，每次「请求-响应」都是独立的，这就会导致身份信息丢失的问题

![](http://mdrs.yuanjin.tech/img/image-20200417161014030.png)

![](http://mdrs.yuanjin.tech/img/image-20200417161244373.png)

这个问题可以使用token令牌解决

![](http://mdrs.yuanjin.tech/img/image-20200417161950450.png)

在使用AJAX时可以按照这样一种通用模式处理：

1. 在处理响应结果时，只要服务器给我的响应头中包含了`token`，就将其保存在`localStorage`中
2. 在请求时，只要`localStorage`中有`token`，就将其代入到响应头发送到服务器。

#### 接口参考

登录：https://yapi.duyiedu.com/project/72/interface/api/105

获取账号信息：https://yapi.duyiedu.com/project/72/interface/api/114

获取聊天记录：https://yapi.duyiedu.com/project/72/interface/api/117

发送聊天消息：https://yapi.duyiedu.com/project/72/interface/api/120

### ChatGPT

#### 准备工作：启动本地服务器

> 已安装好node，以及设置好了npm的淘宝源

**安装依赖**

1. 在命令行进入`chat-server`目录
2. 运行`npm i`

**启动服务器**

1. 在命令行进入`chat-server`目录
2. 运行`npm run dev`

#### 聊天接口

请求路径：`/chat`

请求方法：`POST`

请求消息格式：`application/json`

请求体格式：

```json
// 清除当前的聊天上下文
// 清除后，服务器不再记录之前的聊天内容，后续的聊天将使用新的上下文
{
  "clear": true
}

// 发送消息
{
  "content": "我要你扮演海绵宝宝的魔法海螺壳"
}
```

响应格式：`纯文本`

```text
好的，我现在开始扮演海绵宝宝的魔法海螺壳。我可以说出各种魔法语言和帮助海绵宝宝和派大星解决问题。让我们开始冒险吧！噢呵呵呵呵！
```

注意：响应比较耗时，服务器会流式的将文本发送到客户端，为了提高用户体验，客户端最好流式的接收消息。
