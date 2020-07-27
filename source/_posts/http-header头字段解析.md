---
title: http header头字段解析
date: 2020-07-27 17:01:20
tags: http
categories: http 

---

HTTP头字段（英语：HTTP header fields）是指在超文本传输协议（HTTP）的请求和响应消息中的消息头部分。它们定义了一个超文本传输协议事务中的操作参数。HTTP头部字段可以自己根据需要定义，因此可能在 Web 服务器和浏览器上发现非标准的头字段。
<!-- more -->

### 基本格式

协议头的字段，是在请求（request）或响应（response）行（一条消息的第一行内容）之后传输的。协议头的字段是以明文的字符串格式传输，是以冒号分隔的键名与键值对，以回车(CR)加换行(LF)符号序列结尾。协议头部分的结尾以一个空白字段标识，结果就是，也就是传输两个连续的CR+LF。在历史上，很长的行曾经可能以多个短行的形式传输；在下一行的开头，输出一个空格(SP)或者一个水平制表符(HT)，表示它是一个后续行。在如今，这种换行形式已经被废弃。

### 类型

HTTP 头字段根据实际用途被分为以下 4 种类型：

- 通用头字段(英语：General Header Fields)
- 请求头字段(英语：Request Header Fields)
- 响应头字段(英语：Response Header Fields)
- 实体头字段(英语：Entity Header Fields)

### 字段名

在 RFC 7230、RFC 7231、RFC 7232、RFC 7233、RFC 7234 和 RFC 7235 中，对一组核心字段进行了标准化。有一份关于这些字段的官方登记，以及一些列的补充规范，由IANA（The Internet Assigned Numbers Authority，互联网数字分配机构）维护。各个应用程序也可以自行定义额外的字段名和相应的值。IANA维护的头字段的[永久登记表](https://www.iana.org/assignments/message-headers/message-headers.xml#perm-headers)和[临时登记表](https://www.iana.org/assignments/message-headers/message-headers.xml#prov-headers)。

有时我们可以看到有些字段名称前加有`X-`前缀进行标识，是用来标识这是个非标准的协议头字段，这一惯例已在2012年6月被废弃。同样的曾经有使用`Downgraded-`的限制也在2013年3月被解除。

### 大小限制

标准中没有对每个协议头字段的名称和值的大小设置任何限制，也没有限制字段的个数。然而，出于实际场景及安全性的考虑，大部分的服务器、客户端和代理软件都会实施一些限制。例如，Apache 2.3服务器在默认情况下限制每个字段的大小不得超过8190字节，同时，单个请求中最多有100个头字段。

### 常见请求字段

| 协议头字段名                                            | 说明                                                         | 示例                                                         | 状态       |
| ------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------- |
| Accept                                                  | 能够接受的回应内容类型（Content-Types）                      | Accept: text/plain                                           | 常设       |
| Accept-Charset                                          | 能够接受的字符集                                             | `Accept-Charset: utf-8`                                      | 常设       |
| Accept-Encoding                                         | 能够接受的编码方式列表。参考[HTTP压缩](https://zh.wikipedia.org/wiki/HTTP压缩)。 | `Accept-Encoding: gzip, deflate`                             | 常设       |
| Accept-Language                                         | 能够接受的回应内容的自然语言列表。参考 [内容协商](https://zh.wikipedia.org/wiki/内容协商) 。 | `Accept-Language: en-US`                                     | 常设       |
| Authorization                                           | 用于超文本传输协议的认证的认证信息                           | `Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`          | 常设       |
| [Cache-Control](https://zh.wikipedia.org/wiki/网页快照) | 用来指定在这次的请求/响应链中的所有缓存机制 都必须 遵守的指令 | `Cache-Control: no-cache`                                    | 常设       |
| Content-Length                                          | 以 八位字节数组 （8位的字节）表示的请求体的长度              | `Content-Length: 348`                                        | 常设       |
| Content-Type                                            | 请求体的 多媒体类型 （用于POST和PUT请求中）                  | `Content-Type: application/x-www-form-urlencoded`            | 常设       |
| Date                                                    | 发送该消息的日期和时间(按照 RFC 7231 中定义的"超文本传输协议日期"格式来发送) | `Date: Tue, 15 Nov 1994 08:12:31 GMT`                        | 常设       |
| User-Agent                                              | 浏览器的[浏览器身份标识字符串](https://zh.wikipedia.org/wiki/用户代理) | `User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/21.0` | 常设       |
| Origin                                                  | 发起一个针对 跨来源资源共享 的请求（要求服务器在回应中加入一个‘访问控制-允许来源’（'Access-Control-Allow-Origin'）字段）。 | `Origin: http://www.example-social-network.com`              | 常设: 标准 |
| Cookie                                                  | 之前由服务器通过 Set- Cookie （下文详述）发送的一个 超文本传输协议Cookie | `Cookie: $Version=1; Skin=new;`                              | 常设: 标准 |

### 常见响应字段

| 字段名                                                       | 说明                                                         | 例子                                                         |                            状态                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | :--------------------------------------------------------: |
| Access-Control-Allow-Origin                                  | 指定哪些网站可参与到跨来源资源共享过程中                     | `Access-Control-Allow-Origin: *`                             |                            临时                            |
| Accept-Patch | 指定服务器支持的文件格式类型。                               | `Accept-Patch: text/example;charset=utf-8`                   |                            常设                            |
| Accept-Ranges                                                | 这个服务器支持哪些种类的部分内容范围                         | `Accept-Ranges: bytes`                                       |                            常设                            |
| Age                                                          | 这个对象在代理缓存中存在的时间，以秒为单位                   | `Age: 12`                                                    |                            常设                            |
| Allow                                                        | 对于特定资源有效的动作。针对HTTP/405这一错误代码而使用       | `Allow: GET, HEAD`                                           |                            常设                            |
| [Cache-Control](https://zh.wikipedia.org/wiki/网页快照)      | 向从服务器直到客户端在内的所有缓存机制告知，它们是否可以缓存这个对象。其单位为秒 | `Cache-Control: max-age=3600`                                |                            常设                            |
| Connection                                                   | 针对该连接所预期的选项                                       | `Connection: close`                                          |                            常设                            |
| Content-Disposition                                          | 一个可以让客户端下载文件并建议文件名的头部。文件名需要用双引号包裹。 | `Content-Disposition: attachment; filename="fname.ext"`      |                            常设                            |
| Content-Encoding                                             | 在数据上使用的编码类型。参考 超文本传输协议压缩 。           | `Content-Encoding: gzip`                                     |                            常设                            |
| Content-Language                                             | 内容所使用的语言                                             | `Content-Language: da`                                       |                            常设                            |
| Content-Length                                               | 回应消息体的长度，以 字节 （8位为一字节）为单位              | `Content-Length: 348`                                        |                            常设                            |
| Content-Location                                             | 所返回的数据的一个候选位置                                   | `Content-Location: /index.htm`                               |                            常设                            |
| Content-Range                                                | 这条部分消息是属于某条完整消息的哪个部分                     | `Content-Range: bytes 21010-47021/47022`                     |                            常设                            |
| Content-Type                                                 | 当前内容的[MIME](https://zh.wikipedia.org/wiki/MIME)类型     | `Content-Type: text/html; charset=utf-8`                     |                            常设                            |
| Date                                                         | 此条消息被发送时的日期和时间(按照 RFC 7231 中定义的“超文本传输协议日期”格式来表示) | `Date: Tue, 15 Nov 1994 08:12:31 GMT`                        |                            常设                            |
| Expires                                                      | 指定一个日期/时间，超过该时间则认为此回应已经过期            | `Expires: Thu, 01 Dec 1994 16:00:00 GMT`                     |                         常设: 标准                         |
| Last-Modified                                                | 所请求的对象的最后修改日期(按照 RFC 7231 中定义的“超文本传输协议日期”格式来表示) | `Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT`               |                            常设                            |
| [Location](https://zh.wikipedia.org/wiki/HTTP_Location)      | 用来 进行重定向，或者在创建了某个新资源时使用。              | `Location: http://www.w3.org/pub/WWW/People.html`            |                            常设                            |
| Proxy-Authenticate                                           | 要求在访问代理时提供身份认证信息。                           | `Proxy-Authenticate: Basic`                                  |                            常设                            |
| Refresh                                                      | 用于设定可定时的重定向跳转。右边例子设定了5秒后跳转至“`http://www.w3.org/pub/WWW/People.html`”。 | `Refresh: 5; url=http://www.w3.org/pub/WWW/People.html`      | 专利并非标准Netscape实现的扩展，但大部分网页浏览器也支持。 |
| Retry-After                                                  | 如果某个实体临时不可用，则，此协议头用来告知客户端日后重试。其值可以是一个特定的时间段(以秒为单位)或一个超文本传输协议日期。 | Example 1: `Retry-After: 120`Example 2: `Retry-After: Fri, 07 Nov 2014 23:59:59 GMT` |                            常设                            |
| Server                                                       | 服务器的名字                                                 | `Server: Apache/2.4.1 (Unix)`                                |                            常设                            |
| Set-Cookie                                                   | [HTTP cookie](https://zh.wikipedia.org/wiki/Cookie)          | `Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1`        |                         常设: 标准                         |