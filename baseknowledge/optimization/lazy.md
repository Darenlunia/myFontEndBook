---
description: 这里是手写js实现图片懒加载。目前许多框架已开始提供懒加载组件，例如<vue-lazyload><>react-lazyload>
---

# 图片懒加载

### 方案一:clientHeight、scrollTop 和 offsetTop <a id="&#x65B9;&#x6848;&#x4E00;-clientheight&#x3001;scrolltop-&#x548C;-offsettop"></a>

首先给图片一个占位资源:

```markup
<img src="default.jpg" data-src="http://www.xxx.com/target.jpg" /></img>
```

接着，通过监听 scroll 事件来判断图片是否到达视口:

```javascript
let img = document.getElementsByTagName("img");
let count = 0;//计数器，从第一张图片开始计，设置count可以节省一点性能。

lazyload();//首次加载别忘了显示图片

window.addEventListener('scroll', lazyload);//注意lazyload是绑定在window上

function lazyload() {
  let viewHeight = document.documentElement.clientHeight;//视口高度
  let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;//滚动条卷去的高度
  for(let i = count; i <num; i++) { //每次从count开始算起
    // 元素现在已经出现在视口中
    if(img[i].offsetTop < scrollHeight + viewHeight) {
      if(img[i].getAttribute("src") !== "default.jpg") continue;
      img[i].src = img[i].getAttribute("data-src");
      count ++;
    }
  }
}
```

（主要记住`clientHeight，scrollTop，offsetTop`，前两绑定在`document.documentElement`上，最后一个绑定在`元素`上。）

当然，最好对 scroll 事件做节流处理，以免频繁触发:

```javascript
window.addEventListener('scroll', throttle(lazyload, 200));
```

### 方案二：getBoundingClientRect <a id="&#x65B9;&#x6848;&#x4E8C;&#xFF1A;getboundingclientrect"></a>

现在我们用另外一种方式来判断图片是否出现在了当前视口, 即 DOM 元素的 `getBoundingClientRect` API。

上述的 lazyload 函数改成下面这样:

```javascript
function lazyload() {
  for(let i = count; i <num; i++) {
    // 元素现在已经出现在视口中
    if(img[i].getBoundingClientRect().top < document.documentElement.clientHeight) {
      if(img[i].getAttribute("src") !== "default.jpg") continue;
      img[i].src = img[i].getAttribute("data-src");
      count ++;
    }
  }
}
```

`getBoundingClientRect()`是取当前元素在当前窗口中的位置。

### 方案三: IntersectionObserver <a id="&#x65B9;&#x6848;&#x4E09;-intersectionobserver"></a>

这是浏览器内置的一个`API`，实现了`监听window的scroll事件`、`判断是否在视口中`以及`节流`三大功能。

```javascript
let img = document.document.getElementsByTagName("img");

const observer = new IntersectionObserver(changes => {
  //changes 是被观察的元素集合
  for(let i = 0, len = changes.length; i < len; i++) {
    let change = changes[i];
    // 通过这个属性判断是否在视口中
    if(change.isIntersecting) {
      const imgElement = change.target;
      imgElement.src = imgElement.getAttribute("data-src");
      observer.unobserve(imgElement);
    }
  }
})
observer.observe(img);
```

注意 `IntersectionObserver`是一个专门用于资源懒加载的类。构造函数参数是一个函数。函数的参数是要懒加载的元素（集合）。用`元素.isIntersecting`判断当前元素是否在视口中，已加载出来的元素取消监听。

这样就很方便地实现了图片懒加载，当然这个`IntersectionObserver`也**可以用作其他资源的预加载**，功能非常强大。

