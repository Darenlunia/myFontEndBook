---
description: 主要讲述报文结构、基本概念、二者区别。（主要参考神三元博客）
---

# HTTP/HTTPS

## 1. HTTP报文格式

HTTP 报文是`header + body`的结构，具体而言:

```text
起始行 + 头部 + 空行 + 实体
```

###  起始行

对于请求报文来说，**起始行**类似下面这样:

```text
GET /home HTTP/1.1
```

也就是**方法 + 路径 + http版本**。

对于响应报文来说，**起始行**一般长这样:

```text
HTTP/1.1 200 OK
```

响应报文的起始行也叫做`状态行`。由**http版本+状态码+原因**组成。

值得注意的是，在起始行中，每两个部分之间用**空格**隔开，最后一个部分后面应该接一个**换行**。

### 头部

![](../../.gitbook/assets/tu-pian-%20%2813%29.png)

![](../../.gitbook/assets/tu-pian-%20%288%29.png)

不管是请求头还是响应头，其中的字段是相当多的，而且牵扯到`http`非常多的特性，这里就不列举了。

### 空行

很重要，用来区分开`头部`和`实体`。

### 实体

就是具体的数据了，也就是`body`部分。请求报文对应`请求体`, 响应报文对应`响应体`。

## 2.  HTTP 请求方法

### 有哪些请求方法 <a id="&#x6709;&#x54EA;&#x4E9B;&#x8BF7;&#x6C42;&#x65B9;&#x6CD5;&#xFF1F;"></a>

`http/1.1`规定了以下请求方法\(注意，都是大写\):

* GET: 通常用来获取资源
* HEAD: 获取资源的元信息
* POST: 提交数据，即上传数据
* PUT: 修改数据
* DELETE: 删除资源\(几乎用不到\)
* CONNECT: 建立连接隧道，用于代理服务器
* OPTIONS: 列出可对资源实行的请求方法，用来跨域请求
* TRACE: 追踪请求-响应的传输路径

### GET 和 POST 区别 <a id="get-&#x548C;-post-&#x6709;&#x4EC0;&#x4E48;&#x533A;&#x522B;&#xFF1F;"></a>

首先最直观的是**语义上**的区别，GET常用来获取数据，POST常用来操作数据。其他差别：

* 从**编码**的角度，GET 只能进行 URL 编码，只能接收 ASCII 字符，而 POST 没有限制。
* 从**参数**的角度，GET 一般放在 URL 中，因此不安全，POST 放在请求体中，更适合传输敏感信息。
* 从**缓存**的角度，GET 请求会被浏览器主动缓存下来，留下历史记录，而 POST 默认不会，POST回退会再次发送请求。
* 从**TCP**的角度，GET 请求会把请求报文一次性发出去，而 POST 会分为两个 TCP 数据包，首先发 header 部分，如果服务器响应 100\(continue\)， 然后发 body 部分。\(**火狐**浏览器除外，它的 POST 请求只发一个 TCP 包\)

## 3. URI 的结构

```text
https://www.baidu.com/s?wd=HTTP&rsv_spt=1
```

这个 URI 中，`https`即`scheme`部分，`www.baidu.com`为`host:port`部分（注意，http 和 https 的默认端口分别为80、443），`/s`为`path`部分，而`wd=HTTP&rsv_spt=1`就是`query`部分。

URI 只能使用`ASCII`, ASCII 之外的字符是不支持显示的，而且还有一部分符号是界定符，如果不加以处理就会导致解析出错。**因此**，URI 引入了`编码`机制，将所有**非 ASCII 码字符**和**界定符**转为十六进制字节值，然后在前面加个`%`。如，空格被转义成了`%20`，**三元**被转义成了`%E4%B8%89%E5%85%83`。（闪现三元）

## 4. 状态码

自己先想一下下面这些状态码是否自己知道含义：

* `1开头的：协议处理中的状态，需要后续操作`
* `2开头的：200 204`
* `3开头的：301 302 304`
* `4开头的：400 403 404` 
* `5开头的：500 503`

### 2xx <a id="_2xx"></a>

**`200 OK`**是见得最多的成功状态码。通常在响应体中放有数据。

**`204 No Content`**含义与 200 相同，但响应头后没有 body 数据。

### 3xx <a id="_3xx"></a>

**`301 Moved Permanently`**即永久重定向，对应着**`302 Found`**，即临时重定向。

比如你的网站从 HTTP 升级到了 HTTPS 了，以前的站点再也不用了，应当返回`301`，这个时候浏览器默认会做缓存优化，在第二次访问的时候自动访问重定向的那个地址。

而如果只是暂时不可用，那么直接返回`302`即可，和`301`不同的是，浏览器并不会做缓存优化。

**`304 Not Modified`**: 当协商缓存命中时会返回这个状态码。详见[浏览器缓存](http://47.98.159.95/my_blog/perform/001.html).

### 4xx <a id="_4xx"></a>

**`400 Bad Request`**: 开发者经常看到一头雾水，只是笼统地提示了一下错误，并不知道哪里出错了。

**`403 Forbidden`**: 服务器禁止访问，比如法律禁止、信息敏感。

**`404 Not Found`**: 资源未找到，表示没在服务器上找到相应的资源。

**`405 Method Not Allowed`**: **请求方法**不被服务器端允许。

###  5xx <a id="_5xx"></a>

**`500 Internal Server Error`**: 仅仅告诉你服务器出错了，出了啥错咱也不知道。

**`503 Service Unavailable`**: 表示服务器当前很忙，暂时无法响应服务

## 5. 头部Content系列字段

`Accept`代表`发送端（客户端）`希望接受的数据类型。 比如：`Accept：text/xml;` 代表客户端希望接受的数据类型是xml类型。

`Content-Type`代表`发送端（客户端|服务器）`发送的实体数据的数据类型。 比如：`Content-Type：text/html;` 代表发送端发送的数据格式是html。

二者合起来， `Accept:text/xml； Content-Type:text/html` 即代表希望**接受的数据类型是xml格式，本次请求发送的数据的数据格式是html**。 

![](../../.gitbook/assets/tu-pian-%20%286%29.png)

### 数据格式

HTTP 从**MIME type**取了一部分来标记报文 body 部分的数据类型，这些类型体现在`Content-Type`这个字段，当然这是针对于发送端而言，接收端想要收到特定类型的数据，也可以用`Accept`字段。

具体而言，这两个字段的取值可以分为下面几类:

* `text： text/html, text/plain, text/css 等`
* `image: image/gif, image/jpeg, image/png 等`
* `audio/video: audio/mpeg, video/mp4 等`
* `application: application/json, application/javascript, application/pdf, application/octet-stream等`

在 http 中，**有两种主要的表单提交的方式**，体现在两种不同的`Content-Type`取值:

* `application/x-www-form-urlencoded`
* `multipart/form-data`

 由于表单提交一般是`POST`请求，很少考虑`GET`，因此这里我们将默认提交的数据放在请求体中。

对于`application/x-www-form-urlencoded`格式的表单内容，有以下特点:

* 其中的数据会被编码成以`&`分隔的键值对
* 字符以**URL编码方式**编码。

```text
// 转换过程: {a: 1, b: 2} -> a=1&b=2 -> 如下(最终形式)
"a%3D1%26b%3D2"
```

对于`multipart/form-data`而言:

* 请求头中的`Content-Type`字段会包含`boundary`，且`boundary`的值由浏览器默认指定。例: `Content-Type: multipart/form-data;boundary=----WebkitFormBoundaryRRJKeWfHPGrS4LKe`。
* 数据会分为多个部分，每两个部分之间通过分隔符来分隔，每部分表述均有 HTTP 头部描述子包体，如`Content-Type`，在最后的分隔符会加上`--`表示结束。

相应的`请求体`是下面这样:

```text
Content-Disposition: form-data;name="data1";
Content-Type: text/plain
data1
----WebkitFormBoundaryRRJKeWfHPGrS4LKe
//没有这个空行，这里弄个空行看着方便
Content-Disposition: form-data;name="data2";
Content-Type: text/plain
data2
----WebkitFormBoundaryRRJKeWfHPGrS4LKe--
```

在实际的场景中，对于文件、图片上传，基本采用`multipart/form-data`而不用`application/x-www-form-urlencoded`，因为没有必要做 URL 编码，带来巨大耗时的同时也占用了更多的空间。

### 压缩方式

```text
// 发送端
Content-Encoding: gzip
// 接收端
Accept-Encoding: gizp
```

### 支持语言

```text
// 发送端
Content-Language: zh-CN, zh, en
// 接收端
Accept-Language: zh-CN, zh, en
```

### 字符集

```text
// 发送端
Content-Type: text/html; charset=utf-8
// 接收端
Accept-Charset: charset=utf-8
```

### 其他 

例如大文件传输时使用的Content-Range等。

## 6. HTTP和HTTPS区别

### HTTP优缺点

#### （1） 特点

* 灵活可扩展，可扩展请求头响应头，可传输各种格式数据。
* **可靠传输**。HTTP 基于 TCP传输，这也属于 TCP 的特性。
* 请求-应答。也就是`一发一收`、`有来有回`， 当然这个请求方和应答方不单单指客户端和服务器之间，如果某台服务器作为代理来连接后端的服务端，那么这台服务器也会扮演**请求方**的角色。
* **无状态**。这里的状态是指**通信过程的上下文信息**，而每次 http 请求都是独立、无关的，默认不需要保留状态信息。

#### （2）缺点

* 无状态（cookie记录状态），明文传输（HTTPS解决），[队头阻塞](http://47.98.159.95/my_blog/http/010.html#%E4%BB%80%E4%B9%88%E6%98%AF-http-%E9%98%9F%E5%A4%B4%E9%98%BB%E5%A1%9E%EF%BC%9F)（HTTP1.2版本协议解决）。

### HTTPS基本概念

### TLS

 **TLS1.0 = SSL3.1.**现在主流的版本是 TLS/1.2  or  TLS/1.3。

握手过程：[故事入门](https://mp.weixin.qq.com/s/StqqafHePlBkWAPQZg3NrA)    [抓包查看 ](https://blog.csdn.net/tterminator/article/details/50675540)   [详细过程](https://www.cnblogs.com/barrywxx/p/8570715.html) 

以及后来发现的一个解释的比较好的[故事](https://labuladong.gitbook.io/algo/di-wu-zhang-ji-suan-ji-ji-shu/mi-ma-ji-shu)。[故事](https://labuladong.gitbook.io/algo/di-wu-zhang-ji-suan-ji-ji-shu/mi-ma-ji-shu)。

**1.简略讲讲原理：**对称加密【缺点：需要传递唯一钥匙，易被监听】--&gt;非对称加密RSA（公钥公开，你用我的`公钥加密`给我的文件，我用我的`私钥解开`）【缺点：慢】--&gt;对称+非对称加密【中和二者优缺点】。但非对称加密还有个缺点，中间人攻击--公钥可以被拦截和替换。

这个时候就需要公证处CA的存在了。CA公开自己的公钥，保留私钥，和RSA不同的是用自己的`私钥加密`数据，这就是**数字签名**。**证书就是公钥 + 签名，由第三方认证机构颁发**。引入可信任的第三方，是终结信任循环的一种可行方案。

由于公钥公开，其他人也可以用公钥解开，但是解开也无妨，因为CA的目的不是加密传输，而是证明身份，证明现在发送的信息确实是你发送的，是不可被中间人替换的。不理解的话可以看看[故事](https://labuladong.gitbook.io/algo/di-wu-zhang-ji-suan-ji-ji-shu/mi-ma-ji-shu)。

TLS不能完全预防中间人攻击，但是可以最大程度做到预防。

**2. 简略讲讲过程：**

* 客户端打招呼，发送`加密套件`（加密算法）、`客户端随机数` 等 
* 服务端打招呼，发送`服务端随机数`、`服务端参数`等 
* 服务端把证书发给客户端，客户端进行证书校验（验证公钥）。
* 验证通过后客户端根据`服务端参数`和`客户端参数`生成`pre随机数`，然后把`客户端参数`给服务端。
* 服务端也相同方法生成`pre随机数`。
* 最后`客户端随机数+服务端随机数+pre随机数`构成最终对称秘钥。后续交互都是用这个秘钥。

![](../../.gitbook/assets/tu-pian-%20%2811%29.png)

### 区别

HTTP 是明文传输，HTTPS 通过 SSL\TLS 进行了加密。

HTTP 的端口号是 80，HTTPS 是 443

HTTPS 需要到 CA 申请证书，一般免费证书很少，需要交费

HTTPS 的连接很简单，是无状态的；HTTPS 协议是由 SSL+HTTP 协议构建的可进行加密传输、身份认证的网络协议，比 HTTP 协议安全。

## 7. 常见报文字段

HTTP还有一些常见的请求头、响应头字段。

上面提到了Content系列，另一个重要的还有缓存系列字段，例如响应头的Cache-control（强缓存）、Last-Modified、ETag、Set-Cookie、Origins、Access-control-allow-xxx；请求头的If-Modified-Since、If-None-Match、Access-control-request-xxx等等。

{% embed url="http://47.98.159.95/my\_blog/http/004.html\#\_1xx" %}







