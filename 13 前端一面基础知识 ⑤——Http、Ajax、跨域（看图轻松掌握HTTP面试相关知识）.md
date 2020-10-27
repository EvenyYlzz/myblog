## 前言
&nbsp;&nbsp;&nbsp;&nbsp;根据慕课网的《快速搞定前端技术一面 匹配大厂面试要求》课程所整理的题目，陆续更新。如果你觉得本文相关知识点你已经掌握，你可以查看我之前的相关文章哦
## 常考面试题
### 1.Http常见的状态码有哪些
Http状态码分类
- 1xx 服务器收到请求
- 2xx 请求成功
- 3xx 重定向
- 4xx 客户端错误
- 5xx 服务端错误

**常见状态码**
- 200 请求处理成功
- 204 请求处理成功
- 301 永久性重定向
- 302 临时性重定向
- 303 临时性重定向，并明确表示客户端要用GET方法请求资源
- 304 资源已找到，但客户端缓存资源未过期，仍可使用
- 400 请求报文中存在语法错误
- 401（总共返回两次） ①需要认证（弹出认证窗口）②认证失败
- 403 没有权限，拒绝访问
- 404 无法找到请求的资源（①网址错了 ②服务器拒绝请求，不说明理由）
- 500 服务器内部资源错误
- 503 服务器超载，停机维护
### 2.Http常见的header有哪些
#### 2.1 Request Headers
- ①Accept 浏览器可接收的数据样式
- ②Accept-Encoding 浏览器可接收的压缩算法，如gzip
- ③Accept-Language 浏览器可接收的语言，如zh-CN
- ④Connection：keep-alive 一次TCP链接重复使用
- ⑤Cookie 是用于管理服务端和客户端直接的状态， 其本质上就是一堆存储在客户端的数据
- ⑥Host 用来实现虚拟主机技术
- ⑦User-Agent 简称UA，浏览器信息
- ⑧Content-type 发送数据的格式，如application/json

#### 2.2 Response Headers
- ①Content-type 返回数据的格式
- ②Content-length 返回数据的大小，例如多少字节
- ③Content-Encoding 返回数据的压缩算法，如gzip
- ④Set-Cookie 服务器端向客户端发送 cookie
- ⑤Cache-control 指定缓存机制、缓存逻辑
- ⑥Expires 控制缓存失效的日期（也控制缓存过期，但已经被cache-control替代）
- ⑦Last-Modified 在服务器端最后被修改的时间
- ⑧Eatg 是一个可以与Web资源关联的记号（ETag没改变，则返回状态304然后不返回，这也和Last-Modified一样。测试Etag主要在断点下载时比较有用）

#### 2.3 缓存相关的Headers
- ①Cache-control
- ②Expires
- ③Last-Modified 
- ④If-Modified-Since 从浏览器作为请求头发出，与服务器端的Last-Modified作比较，时间一致则返回304继续使用，时间不一致则使用从服务器端返回的最新资源
- ⑤Etag
- ⑥If-None-Match 当你第一次发起HTTP请求时，服务器会返回一个Etag，并在你第二次发起同一个请求时，客户端会同时发送一个If-None-Match，而它的值就是Etag的值（此处由发起请求的客户端来设置）。然后，服务器会比对这个客服端发送过来的Etag是否与服务器的相同，如果相同，就将If-None-Match的值设为false，返回状态为304，客户端继续使用本地缓存，
### 3.描述一下Http的缓存机制
#### 3.1 什么是缓存？
打开一个网站，不需要再重新向服务端获取的资源，因为该资源未改变，则该资源称作缓存
#### 3.2 哪些资源可以被缓存？
静态资源（JS、CSS、Img）
#### 3.3 ①Http缓存——强制缓存
由服务器端返回的（cache-control：xxx值）字段控制强制缓存逻辑
- ①max-age 以秒为单位，控制资源可以持续使用的时长为多少秒
- ②no-cache 强制客户端直接向服务器发送请求,也就是说每次请求都必须向服务器发送。服务器接收到请求，然后判断资源是否变更，是则返回新内容，否则返回304，未变更。这个很容易让人产生误解，使人误以为是响应不被缓存。实际上Cache-Control: no-cache是会被缓存的，只不过每次在向客户端（浏览器）提供响应数据时，缓存都要向服务器评估缓存响应的有效性
- ③no-store 禁止一切缓存（这个才是响应不被缓存的意思）
- ④private 只能针对个人用户，而不能被代理服务器缓存
- ⑤public 指示响应可被任何缓存区缓存
#### 3.4 ②Http缓存——协商缓存（对比缓存，是服务器端缓存策略）
协商缓存，也叫对比缓存。浏览器初次请求之后，服务器会返回资源和资源标识，然后浏览器第二次发出请求时，则会带着资源标识发到服务器端。服务器端会对接收到的资源标识与服务器上的资源标识进行对比，如果一致则返回304标识资源可继续使用，如果不一致则返回200和最新的资源

使用较多的两种资源标识</br>
①Last-Modified 和 对应的 If-Modified-Since （资源的最后修改时间）
![](https://img-blog.csdnimg.cn/img_convert/8a13cded098639b5f16cc0fe5e351749.png)
②Etag 和 对应的 If-None-Match （资源的唯一标识，类似人的指纹）
![](https://img-blog.csdnimg.cn/img_convert/411aa2178992ccc128b8c9631d03c791.png)

③对比Last-Modified 和 Etag
- 优先使用Etag，因为它是资源唯一标识
- Last-Modified 只能精确到秒级
- 如果资源被重复生成，而内容不变，则Etag会更准确
#### 3.5 ③【Http缓存——综述】
首先浏览器发出Http请求，假设有cache-control控制强制缓存，先判断缓存是否过期，例如是max-age
- ①在规定时间内没有过期那么就会直接读取上次缓存的资源，直接页面呈现。假设强制缓存过期了，那么就会判断是否有Etag和Last-Modified协商缓存字段，有的话就会带着If-None-Match，If-Modified-Since字段发送请求，然后服务器接收后判断缓存是否还可继续使用
- ②可以使用则返回304然后读取缓存，页面呈现
- ③如果不可使用那么就会返回200和最新的资源，然后页面呈现
- ④如果没有Etag和Last-Modified协商缓存字段，那么就会直接发起Http请求，请求最新的资源，然后服务器返回最新的资源，直接页面呈现
![](https://img-blog.csdnimg.cn/img_convert/8666987269bcba554e001951754aea45.png)
### 4.手写一个简易的Ajax
Ajax技术的核心是`XMLHttpRequest`对象。对于`XMLHttpRequest`这里不做详解，只讲解手写方法中涉及的部分知识点。

#### 4.1 open()方法
open()方法用于初始化一个请求。open()方法接收三个参数：
- 第一个参数 method：要发送的请求的类型。比如GET、POST、PUT、DELETE等。
- 第二个参数 url：请求的URL。
- 第三个参数 async：是否异步发送请求的布尔值。true为异步发送请求。

#### 4.2 readyState属性
readyState属性表示请求/响应过程的当前活动阶段。这个属性的值如下：
- 0（`UNSENT`）未初始化。尚未调用open()方法
- 1（`OPENED`）启动。已经调用open()方法，但没有调用send()方法
- 2（`HEADERS_RECEIVED`）发送。已经调用send()方法，但尚未接收到响应
- 3（`LOADING`）接收。已经接收到部分响应数据
- 4（`DONE`）完成。已经接收到全部响应数据

```javascript
function ajax ( url , successFn ){

    const xhr = new XMLHttpRequest()
    xhr.open( 'GET' , url , true)
    //	true异步，避免卡住， false为同步

    xhr.onreadystatechange = function() {
        if( xhr.readyState == 4 ){
            if( xhr.status == 200 ){
                successFn(xhr.responseText)
            }
        }
    }

    xhr.send(null)
}
```
### 5.手写一个Ajax要求结合promise
```javascript
function ajax(url) {
    const p = new Promise((resolve, reject) => {
    
        const xhr = new XMLHttpRequest();
        xhr.open("GET", url, true);
        //	true异步，避免卡住， false为同步
        
        xhr.onreadystatechange = function () {
            if (xhr.readyState === 4) {
            
                if (xhr.status === 200) {
                    resolve(JSON.parse(xhr.responseText));
                } else if (xhr.status === 404) {
                    reject(new Error("404 not found"));
                }
                
            }
        };
        
        xhr.send(null);
    });
    return p;
}
```
### 6.跨域是什么，为什么会产生跨域问题
出于浏览器的同源策略限制，浏览器会拒绝跨域请求

同源策略</br>
- ajax请求时，浏览器要求当前网页和server服务端必须同源（安全问题）
- 同源：即协议、域名、端口三者必须一致
- 例如：前端：`http://a.com:8080/`，后端：`https://b.com/api/xxx`，此时前端的协议为`http`、域名为`a.com`、端口为`8080`，而后端的协议为`https`、域名为`b.com`、端口没写但https默认为`80`端口，所以三者都不一样，只要有一个不一样，浏览器就会截获请求，这就是跨域问题
### 7.如何解决跨域问题
所有的跨域，都必须经过server端允许和配合
未经server端允许就实现跨域，说明浏览器有漏洞，是一个危险的信号
常见解决跨域的方式有JSONP、CORS和代理跨域，这里只对JSONP做简单讲解

JSONP
- ①`<script>`可跨过跨域限制
- ②服务器可以任意动态拼接数据返回
- ③所以，`<script>`就可以获得跨域的数据，只要服务端愿意配合返回

简单举例，HTML文件
```javascript
<script>
	window.callback = function (data) {
    	console.log(data)
    }
</script>
<script src="http://localhost:8080/jsonp.js"></script>
```
JSONP.js文件
```javascript
callback({ name: "zhangsan" })
```

跨域详解，可以参考这篇文章[深入跨域问题](https://juejin.im/post/6844903607070900238)
### 8.描述cookie、localStorage、sessionStorage的区别
cookie
- ①本身用于浏览器和server通讯
- ②被借用到本地存储，可通过`document.cookie=" "`设置
缺点：
- ①存储大小为最大4KB
- ②Http请求时需要发送到服务端，增加请求数据量

localStorage和sessionStorage
- ①HTML5专门为存储而设计的，最大可存5M
- ②API简单易用，`localStorage.setItem(' ',' ')`、`localStorage.getItem('')`、`sessionStorage.setItem(' ',' ')`、`sessionStorage.getItem('')`
- ③不会随着Http请求被发送出去
- ④区别：`localStorage`永久有效，除非手动从控制台删除，`sessionStorage`存在于当前会话，浏览器关闭则会清除

## 写在文末
&nbsp;&nbsp;&nbsp;&nbsp;如果你觉得我写得还不错的话，可以给我点个赞哦^^，如果哪里写错了、写得不好的地方，也请大家评论指出，以供我纠正。（这个系列也快完结了哦，可以复习复习前面的）
## 其它文章
- [前端一面基础知识 ④——事件与DOM](https://juejin.im/post/6877461224708112397)
- [前端一面基础知识 ③——异步（面试场景题）](https://juejin.im/post/6874852295348060167)
- [前端一面基础知识 ②——作用域和闭包（面试场景题）](https://juejin.im/post/6869272556654821389)
- [前端一面基础知识 ①——CSS面试题](https://juejin.im/post/6865946058795515918)
- [面筋系列①——滴滴sp一面面试真题](https://juejin.im/post/6859957035321016328)
- [面筋系列②——滴滴实习生一面凉经](https://juejin.im/post/6863728442987741191)
