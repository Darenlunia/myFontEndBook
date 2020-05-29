---
description: 缓存一般指get请求缓存，post一般不缓存。接下来以三个部分来把浏览器的缓存机制说清楚：强缓存，协商缓存，缓存位置。
---

# 浏览器缓存

## 1. 强缓存

浏览器中的缓存作用分为两种情况，一种是需要发送`HTTP`请求，一种是不需要发送。

首先是检查强缓存，这个阶段`不需要`发送HTTP请求。

在`HTTP/1.0`和`HTTP/1.1`当中，这个字段是不一样的。在早期，也就是`HTTP/1.0`时期，使用的是**Expires**，而`HTTP/1.1`使用的是**Cache-Control**。让我们首先来看看Expires。

### Expires

`Expires`即过期时间，存在于**服务端返回的响应头**中，告诉浏览器在这个过期时间之前可以直接从缓存里面获取数据，无需再次请求。比如下面这样:

```text
Expires: Wed, 22 Nov 2019 08:41:00 GMT
```

表示资源在`2019年11月22号8点41分`过期，过期了就得向服务端发请求。

这个方式看上去没什么问题，合情合理，但其实潜藏了一个坑，那就是**服务器的时间和浏览器的时间可能并不一致**，那服务器返回的这个过期时间可能就是不准确的。因此这种方式很快在后来的HTTP1.1版本中被抛弃了。

### Cache-Control

在HTTP1.1中，采用了一个非常关键的字段：`Cache-Control`。这个字段也是存在于

它和`Expires`本质的不同在于它并没有采用`具体的过期时间点`这个方式，而是采用过期时长来控制缓存，对应的字段是**max-age**。比如这个例子:

```text
Cache-Control:max-age=3600
```

代表这个响应返回后在 3600 秒，也就是一个小时之内可以直接使用缓存。

除了`max-age`还有一些其他属性，可以互相组合，主要有： 

**public**: 客户端和代理服务器都可以缓存。

**private**： 这种情况就是只有浏览器能缓存了，中间的代理服务器不能缓存。

**no-cache**: 跳过当前的强缓存，发送HTTP请求，即直接进入`协商缓存阶段`。

**no-store**：非常粗暴，不进行任何形式的缓存。

**s-maxage**：这和`max-age`长得比较像，但是区别在于s-maxage是针对代理服务器的缓存时间。

**must-revalidate**: 是缓存就会有过期的时候，加上这个字段一旦缓存过期，就必须回到源服务器验证。

当**Expires**和**Cache-Control**同时存在的时候，**Cache-Control**会优先考虑。

当`强缓存`过期，就会进入`协商缓存`↓。

## 2. 协商缓存

强缓存失效之后，浏览器和服务器使用指定字段核对来判断是否继续使用以前的缓存，就是**协商缓存**。

协商方式由第一次请求时的服务器响应头指定，分为两种：**`Last-Modified`**和**`Etag`**。

### Last-Modified

即最后修改时间。在浏览器第一次给服务器发送请求后，`服务器`会在`响应头`中加上`Last-Modified`。

`浏览器`接收到后，如果再次请求，会在`请求头`中携带`If-Modified-Since`字段，这个字段的值也就是服务器传来的最后修改时间。

服务器拿到请求头中的`If-Modified-Since`的字段后，其实会和这个服务器中`该资源的最后修改时间`对比:

* 如果请求头中的这个值小于最后修改时间，说明是时候更新了。返回新的资源，跟常规的HTTP请求响应的流程一样。
* 否则`返回304`，告诉浏览器直接用缓存。

### ETag

`ETag` 是`服务器`根据当前文件的内容，给文件生成的唯一标识，只要里面的内容有改动，这个值就会变。`服务器`通过`响应头`把这个值给浏览器。

`浏览器`接收到ETag的值，会在下次请求时，将这个值作为`If-None-Match`这个字段的内容，并放到`请求头`中，然后发给服务器。

`服务器`接收到If-None-Match后，会跟服务器上该资源的`ETag`进行比对:

* 如果两者不一样，说明要更新了。返回新的资源，跟常规的HTTP请求响应的流程一样。
* 否则`返回304`，告诉浏览器直接用缓存。

### 两者对比

1. 在`精准度`上，`ETag`优于`Last-Modified`。优于 ETag 是按照内容给资源上标识，因此能准确感知资源的变化。而 Last-Modified 就不一样了，它在一些特殊的情况并不能准确感知资源变化，例如

* Last-Modified 能够感知的单位时间是秒，如果文件在 1 秒内改变了多次，那么这时候的 Last-Modified 并没有体现出修改了。

1. 在性能上，`Last-Modified`优于`ETag`，也很简单理解，`Last-Modified`仅仅只是记录一个时间点，而 `Etag`需要根据文件的具体内容生成哈希值。

另外，如果两种方式都支持的话，服务器会优先考虑`ETag`。

## 缓存位置

浏览器中的缓存位置一共有四种，按优先级从高到低排列分别是：

* Service Worker
* Memory Cache
* Disk Cache
* Push Cache

### 总结 <a id="&#x603B;&#x7ED3;"></a>

对浏览器的缓存机制来做个简要的总结:

首先通过 `Cache-Control` 验证强缓存是否可用

* 如果强缓存可用，直接使用
* 否则进入协商缓存，即发送 HTTP 请求，服务器通过请求头中的`If-Modified-Since`（对应响应头Last-Modified）或者`If-None-Match`（对应响应头ETag）这些条件请求字段检查资源是否更新。
* 若资源更新，返回资源和200状态码
* 否则，返回304，告诉浏览器直接从缓存获取资源
