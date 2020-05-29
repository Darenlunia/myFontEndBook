# Cookie

### 基本概念

HTTP无状态，可以用Cookie携带一些状态信息。本质上是浏览器存储的一个最大4k的文本文件。

cookie键值对的方式存储，在chrome控制台的application中可以看到。

向同一域名发送请求，都会携带相同的cookie，目的是记载客户端的状态，传递给服务端。

服务端可以通过`set-cookie响应头`写入cookie值。cookie的`HttpOnly`属性设置为false的时候，前端也可以通过js获取cookie中的值。

也就是说cookie可以由前端也可以由后端创建，一般由前后端开发人员协商生成cookie 方法。

例如vue-element-admin，登录身份验证后由后端向前端传递token，前端将token存储在cookie中，保证页面刷新不丢失身份。

又例如后端可以通过response中的Set-Cookie 响应头，直接设置。

### Cookie 属性 <a id="cookie-&#x5C5E;&#x6027;"></a>

#### 生存周期 <a id="&#x751F;&#x5B58;&#x5468;&#x671F;"></a>

* **Expires** 指定过期时刻。
* **Max-Age** 指定从现在开始Cookie还会存在的秒数， 秒数过完则cookie过期.

#### 作用域 <a id="&#x4F5C;&#x7528;&#x57DF;"></a>

关于作用域也有两个属性: **Domain**和**path**

Cookie 绑定了域名和路径，在**发送请求**之前，发现域名或者路径和这两个属性不匹配，那么就不会带上 Cookie。值得注意的是，对于路径来说，`/`表示域名下的任意路径都允许使用 Cookie。

#### 安全相关 <a id="&#x5B89;&#x5168;&#x76F8;&#x5173;"></a>

如果 cookie 字段带上`HttpOnly`，表示前端JS 不能访问cookie，这是预防 XSS 攻击的重要手段。

相应的，对于 CSRF 攻击的预防，也有`SameSite`属性，三个值，`Strict`、`Lax`和`None`。

### Cookie的缺点

* 存储容量小 
* 每次跟随域名传输会造成性能浪费 
* 安全问题

### **（重要）**和localStorage、sessionStorage区别

#### 1. localStorage

* 容量。localStorage 的容量上限为**5M**，相比于`Cookie`的 4K 大大增加。当然这个 5M 是针对一个域名的，因此对于一个域名是持久存储的。
* 只存在客户端，默认不参与与服务端的通信。这样就很好地避免了 Cookie 带来的**性能问题**和**安全问题**。
* 接口封装。通过`localStorage`暴露在全局，并通过它的 `setItem` 和 `getItem`等方法进行操作，非常方便。

`localStorage`其实存储的都是字符串，如果是存储对象需要调用`JSON`的`stringify`方法，并且用`JSON.parse`来解析成对象。

**※应用：** 利用`localStorage`的较大容量和持久特性，可以利用`localStorage`存储一些内容稳定的资源，比如官网的`logo`，存储`Base64`格式的图片资源等。

#### 2. sessionStorage

* 容量。容量上限也为 5M。
* 只存在客户端，默认不参与与服务端的通信。
* 接口封装。除了`sessionStorage`名字有所变化，存储方式、操作方式均和`localStorage`一样。

但`sessionStorage`和`localStorage`有一个本质的区别，那就是前者只是会话级别的存储，并不是持久化存储。会话结束，也就是页面关闭，这部分`sessionStorage`就不复存在了。

**※应用：**

* 可以用它对表单信息进行维护，将表单信息存储在里面，可以保证页面即使刷新也不会让之前的表单信息丢失。
* 可以用它存储本次浏览记录。如果关闭页面后不需要这些记录，用`sessionStorage`就再合适不过了。事实上微博就采取了这样的存储方式。

### 其他

vue-element-admin的登录和权限验证以及动态路由生成。

`登录:  前端将账号密码post到服务端进行验证,如果验证通过,服务端返回一个token,拿到token后(将token存入cookie中,保证刷新页面后记住用户的登录状态),根据token再去拉去一个user_info的接口来获取用户详情信息(如用户权限,用户名等等)`

`权限验证: 通过token获取用户对应的role,动态根据用户的role算出对应有权限的路由,通过router.addRoutes动态挂载这些路由.`

