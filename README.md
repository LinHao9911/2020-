2020春招前端面试题总结
# http相关  

1.https与http区别(百度)

```$javascript

http是基于tcp协议的，https是基于ssl协议的，ssl协议在tcp协议的基础上的做了一层加密认证。
https比http传输数据更安全，http是明文传递数据，不安全；而https传输数据经过加密。
https需要证书来验证身份，证书需要购买，http不需要。
https默认端口号是443，http默认是80。
https不能向http发出请求。

了解https通讯过程：

（1）客户使用https的URL访问Web服务器，要求与Web服务器建立SSL连接。

（2）Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。

（3）客户端的浏览器与Web服务器开始协商SSL连接的安全等级，也就是信息加密的等级。

（4）客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。

（5）Web服务器利用自己的私钥解密出会话密钥。

（6）Web服务器利用会话密钥加密与客户端之间的通信

```

2.http请求过程及拿到响应后的渲染过程？（苏宁）
 
```$javascript

http请求过程：
1.浏览器检查当前请求的url是否存在强缓存？命中强缓存，则缓存中读取。
2.dns解析：
  a.浏览器缓存的dns解析，是否存在。
  b.操作系统DNS缓存中搜索
  c.本地host进行dns解析。
  d.向dns服务器发起请求进行dns解析
3.建立tcp链接，三次握手。
4.浏览器发出http请求
5.服务器分析路由，处理请求，返回响应。
6.浏览器接受响应，渲染页面，构建dom
7。关闭tcp链接（4次挥手）  

渲染过程：
1.解析html文件，浏览器按照文档流顺序，构建dom树。（深度遍历，当所有子节点都构建好后，才会去构建当前节点的下一个兄弟节点）
2.浏览器解析css形成css规则树。
3.根据dom树和css规则树，构造出渲染树。
   渲染树：浏览器可以知道网页中有那些节点，各个节点的css以及他们从属关系
   
4.遍历渲染树，计算每个节点位置大小信息，然后进行绘制。

扩展：重绘 和 回流
重绘：dom的一部分进行重画，不影响整体布局。
触发：css背景色...
回流：重新计算渲染树，重新遍历渲染树，重新渲染，浪费性能；
触发：宽高、字体、resize等。
优化：利用文档碎片document.createDocumentFragment并不是真正的dom，操作dom；


```
3.http缓存机制。(百度、滴滴都有问)

[点击查看详细浏览器缓存技术，面试难不倒](https://juejin.im/post/5b9346dcf265da0aac6fbe57)
```$javascript
强缓存： 
    响应报文头：
    catch-control：max-age=212121； 最长生效时常。
    expires:服务器绝对时间
    
    客户端发请求前，会用当前客户端时间与 （上次请求时间+max-age）进行比较，命中则走缓存。
    expires是一个绝对时间，采用的是服务器的。客户端发送请求前，用客户端时间与这个时间进行比较，命中则走缓存。
    max-age优先级比expires高，并且更加靠谱；
    
弱缓存（协商缓存）:
    响应报文头:
    last-modified：最后一次修改时间
    Etag：当前请求的资源生成的一个唯一标识，只要资源不一样这个串就不一样
    
    请求报文头：
    if-modified-since:缓存中文件的最后修改时间
    if-None-Match:缓存中文件的etag
    
    
    客户端发出请求，携带if-modified-since，if-None-Match字段，服务器收到请求 与当前文件的最后修改时间和
    重新生成etag进行比较，命中则返回304； 
    如果命中if-none-match，返回304的响应头中，会携带服务器最新生成的etag;
    如果命中if-modified-since则304响应头中不会携带last-modified，因为最后修改时间没有改变。
    
    注意：etag优先级比 last-modified要高。但是各有优缺点；
        分布式系统里多台机器文件的last-modified必须保持一致，要不负载均衡到不同机器导致对比失败。
        分布式系统计量关闭掉etag，因为每台服务器生成的etag都不一样。
   
    
    使用：协商缓存需要配合强缓存使用，
   
```
4.http get和post区别？（马蜂窝）

```$javascript

GET在浏览器回退时是无害的，而POST会再次提交请求。

GET产生的URL地址可以被Bookmark，而POST不可以。

GET请求会被浏览器主动cache，而POST不会，除非手动设置。

GET请求只能进行url编码，而POST支持多种编码方式。

GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。

GET请求在URL中传送的参数是有长度限制的，而POST么有。
get传送的数据量较小，不能大于2KB。post传送的数据量较大

对参数的数据类型，GET只接受ASCII字符，而POST没有限制。

GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。 

GET参数通过URL传递，POST放在Request body中。

GET产生一个TCP数据包；POST产生两个TCP数据包
对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；
而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。

```


5.http状态码（百度，水滴筹，今日头条）
```$javascript
200 成功
5** 服务器
304 缓存
301 永久重定向
302 临时重定向
400 Bad Request，通用的客户端错误状态，当其他4XX响应代码不适用时，就采用400
401 权限不够访问该资源。
404 not found 当客户端所请求的URI不对应于任何资源时，发送此响应代码，找不到资源。
403 Forbidden 服务器拒绝访问

```

6.列举你所知道的请求头中的字段和响应头中的字段（百度）
```$javascript
请求头
Accept: application/json, text/javascript, */*; q=0.01 //告诉服务器,客户端支持的数据类型
Content-Type: application/x-www-form-urlencoded; charset=UTF-8 //告诉服务器,发送的数据类型
Origin: https://baidu.com            //源
Referer: https://baidu.com/          //源页面
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36
// 浏览器信息
cookie:'',                  //cookie信息
if-modified-since:xxxxx          //上次请求响应头返回的最后修改时间
if-none-match:xxx               //上次请求响应头返回的etag信息



options请求
 Access-Control-Request-Method：，用来列出浏览器的CORS请求会用到哪些HTTP方法
 Access-Control-Request-Headers：浏览器CORS请求会额外发送的头信息字段


响应头：
Access-Contronl-Allow-origin:* ，//允许源访问
access-control-allow-methods：get ,post //允许请求方式
access-control-allow-headers：'name'    //允许携带的请求头
access-control-expose-headers：'name'    //允许浏览器读取的头信息
access-control-allow-credentials:true //表示服务器允许浏览器携带cookie发送
content-type: application/json         响应数据格式
date: Tue, 02 Apr 2019 16:30:02 GMT   //服务器时间
catch-control:max-age=36000             //强缓存最强生效时长
expires:xx-xx-xx                        //服务器失效绝对时间
last-modified：xxx-xxx-xx                //服务器最后修改时间
etag：xxxxxx                             //服务器资源唯一标示符          

options
  Access-Control-Max-Age：秒,                      本次预检请求的有效期



```



7.复杂请求的与简单请求的区别 
 [阮一峰详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
  复杂请求 会比 简单请求多一个options请求（预检查请求）
```$javascript
 cors请求分两种:简单请求 和非简单请求
 
 ==============================================
 简单请求：
  浏览器请求头中会默认增加origin字段，表示来自哪个源
  服务器接收请求后，根据指定的源作出回应。
  响应头中：Access-Control-Allow-Origin:* || domain
         Access-Control-Allow-Credentials:true， //表示是否允许跨域携带cookie。（后续进行讲解）
         Access-Control-Expose-Headers：xxx      //指定浏览器可以获取到的 自定义响应头
 
 
 =============================================        
 非简单请求：
  出现条件：请求方法是PUT或DELETE，
          Content-Type字段的类型是application/json
          增加额外的header字段；   
  非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"options请求（preflight）
  
  浏览器发出options请求头：
    origin:源
    Access-Control-Request-Method：，用来列出浏览器的CORS请求会用到哪些HTTP方法
    Access-Control-Request-Headers：浏览器CORS请求会额外发送的头信息字段
    
  服务器返回options响应头：
    Access-Control-Allow-Origin: http://api.bob.com
    Access-Control-Allow-Methods: GET, POST, PUT    服务器支持的所有跨域请求的方法
    Access-Control-Allow-Headers: X-Custom-Header   表示服务器支持的所有头信息字段
    Access-Control-Max-Age：秒,                      本次预检请求的有效期
    
    
  服务器如果否定了options请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。浏览器就
  会认定，服务器不同意预检请求，因此触发一个错误，被XMLHttpRequest对象的onerror回调函数捕获。  
   
 
  ====================================
  
  withCredentials属性
  cors请求不发送cooki，如果要发送cookie到服务器，一方面要服务器同意。
  Access-Control-Allow-Origin：不能设置*号，必须指定和明确的、与请求网页一致的域名
  Access-Control-Allow-credentials：true；//这个只能设为true；如果不需要cookie，则删除该字段即可。
  
  另一方面，浏览器ajax需要打开 withCredentials      
  var xhr = new XMLHttpRequest();
  xhr.withCredentials = true; 
  
  特点：Cookie依然遵循同源政策，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，
  且（跨源）原网页代码中的document.cookie也无法读取服务器域名下的Cookie。
  
  a.com 请求b.com ，设置了此属性，携带的cookie也只能是b.com 这个域下的cookie；
         
         
```

8.http2 和http1.1的区别

```$javascript
[区别详解](https://juejin.im/post/5a4dfb2ef265da43305ee2d0)
1.首部压缩，使报头更紧凑，更快速传输
2.多路复用，
在HTTP1.1中，浏览器客户端在同一时间，针对同一域名下的请求有一定数量的限制。超过限制数目的请求会被阻塞
http2中，只使用一个链接即可并行发送多个请求和响应，而且互不影响

3.支持服务器推送
如果一个请求是由你的主页发送的，服务器可能会响应主页内容、logo以及样式表，因为他知道客户端会用到这些东西。
这样不但减轻了数据传送冗余步骤，也加快了页面响应的速度，提高了用户体验。


```




# js基础相关

1.js继承？原型链查找原理。（蚂蚁金服）
```$javascript
function inheritPrototype(subType, superType){ 
    // 继承父类的原型
    var prototype = Object.create(superType.prototype);
    // 重写被污染的construct
    prototype.constructor = subType; 
    // 重写子类的原型  
    subType.prototype = Object.assign(prototype, subType.prototype); 
}

```



2.es6 class继承如何实现？（蚂蚁金服）

3.var let const 区别 （基本每个公司都问）

4.promise的特点，使用及原理。（基本每个公司都问）

[手写promise](https://segmentfault.com/a/1190000013871753)
```$javascript

特点：then链式调用
     catch捕获
     all方法
     race方法
     promise对象三种状态 pendding,fulfilled,rejected;
     状态改变，只能改一次；
     promise对象已经是成功状态或是失败状态时，都可以继续通过then传入函数，会通过当前的状态，来决定执行成功还失败，并且把结果或是错误传给相应的函数。
      
原理：状态机 + then方法返回新的promise2对象实现链式调用；

使用
new promise((resolve,reject)=>{
    setTimeout(()=>{
        resolve('成功')
    })
}).then(()=>{},()=>{}).catch(e=>{

})
Promise.all([promise1,promise2]).then();
Promise.race([promise1,promise2]).then();
Promise.resolve(1)
Promise.reject(1)
Promise.deferred();

```


5.generator函数的使用及原理 （蚂蚁金服）

[generator](https://segmentfault.com/a/1190000014028204)

```$javascript

generator 函数可以控制函数的执行，类似将函数分割成多个小函数，依次执行；
用 * 表示一个generator 函数，用 yield 控制函数执行，并且产出；
generator是有两部分组成；* 表示 的函数 是生成器，函数执行的返回结果是迭代器；
迭代器.next();会将yield的值 返回，{value:'返回值'，done：'是否迭代完成'}

使用：

function *read(){
    console.log(1);
    var content1 = yield 'qs';
    console.log(content1);
    var content2 = yield '9';![图片描述][1]
    console.log(content2);
    return content2;
}
var it = read();
var a = it.next() //输出 1  a:{value:'qs',done:false}
var b = it.next('hello')//输出hello  b:{value:'9',done:false}
var c = it.next('generator')//输出generator  c:{value:'generator',done:true}
var d = it.next('123')//输出generator  c:{value:undefined,done:true}
console.log(a,b,c,d)


使用：一般和promise结合使用；

function *g(){
    let result1 = yield ajax('1');
    let result2 = yield ajax(result1);
    return result2;
}

let ite = g();
ite.next().value.then(res=>{
    ite.next(res).value.then(res=>{
        let result = ite.next(res).value;
        console.log(result)
    })
})



//为了更好的结合promise使用，出现了co库；
//把一个生成器函数的迭代器，迭代完成后，统一执行一个成功回调；

let co = require('co');

function *g(){
    let result1 = yield ajax('1');
    let result2 = yield ajax(result1);
    return result2;
}
co(g).then(res=>{console.log(res)});

//co库的原理：利用next产出值的done的状态，去判断是否需要再次递归执行next方法。


function co(g){
    let ite = g();
    return new Promise((resolve,reject)=>{
        function next(data){
            let {value,done} = item.next(data)
            if(!done){
                value.then(res=>{
                    next(res)
                },reject)
            }else{
                resolve(value)
            }
        }
        next();
    })
}






```






6.es6 解构赋值和数组的操作方法

7.讲一下js循环事件池event loop，微任务和宏任务 （boss直聘、小米，苏宁）

[浏览器event loop 和node event-loop](https://juejin.im/post/5aa5dcabf265da239c7afe1e)

浏览器的event loop
```$javascript
js执行会存在一个执行栈，当前执行的同步代码都会放到执行栈中执行(宏任务)；
同步代码执行过程中，如果遇到异步代码（settIimeout、promise等）,会将他们挂起，继续执行同步代码；
同步代码执行完毕后，执行栈清空，会去查看微任务队列是否有可执行函数，然后拿到执行栈执行；
当微任务队列执行完成后， 会去宏任务取一个任务放到执行栈执行，执行完成后再去查看微任务是不是有可执行函数，
有的话取到执行栈执行；如此循环下去，就是event loop；

挂起的异步代码，可执行后，会放到异步任务队列；异步任务队列分为两种队列，一种是微任务(promise,process.nextTick)
一种是宏任务（setTimeout等）；
微任务包括 process.nextTick ，promise ，MutationObserver。
宏任务包括 script ， setTimeout ，setInterval ，setImmediate ，I/O ，UI rendering。

过程：
执行完主执行线程中的任务。
取出Microtask Queue中任务执行直到清空。
取出Macrotask Queue中一个任务执行。
取出Microtask Queue中任务执行直到清空。
重复3和4。




```



node环境 event loop 是区分阶段的；

```$javascript

按照我们的循环的6个阶段依次执行，每次拿出当前阶段中的全部任务执行，清空NextTick Queue，清空Microtask Queue。再执行下一阶段，全部6个阶段执行完毕后，进入下轮循环。即：

清空当前循环内的Timers Queue，清空NextTick Queue，清空Microtask Queue。
清空当前循环内的I/O Queue，清空NextTick Queue，清空Microtask Queue。
清空当前循环内的Check Queu，清空NextTick Queue，清空Microtask Queue。
清空当前循环内的Close Queu，清空NextTick Queue，清空Microtask Queue。
进入下轮循环。

*注意：如果在timers阶段执行时创建了setImmediate则会在此轮循环的check阶段执行，如果在timers阶段创建了setTimeout，由于timers已取出完毕，则会进入下轮循环，check阶段创建timers任务同理。

console.log('1');
setTimeout(function() {
    console.log('2');
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5');
        setTimeout(function(){console.log(11)})
    })
    process.nextTick(function () {
        console.log(10)
    })
    setImmediate(function () {
        console.log(12)
    })
},500);

setTimeout(function() {
    console.log('6');
    new Promise(function(resolve) {
        console.log('7');
        resolve();
    }).then(function() {
        console.log('8')
    })
    process.nextTick(function () {
        console.log(9)
    })
},500)
// 第一次事件环
//timer 阶段 1，2，4，6，7，; nexttick:10,9;//微任务队列  5，8
//io 阶段 无
//check 阶段 12
//close 阶段 无

//第二次事件环
//timer 阶段 11;
//io 阶段 无
//check 阶段
//close 阶段

```

```$javascript

两种环境对比：
console.log('1');
setTimeout(function() {
    console.log('2');
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5'); 
        setTimeout(function(){console.log(100)})
    })
},500)
   
setTimeout(function() {
    console.log('6');
    new Promise(function(resolve) {
        console.log('7');
        resolve();
    }).then(function() {
        console.log('8')
    })
},500)

浏览器执行：
1，2，4，5，6，7，8 100

node
1，2，4，6，7，5，8，100；



console.log(1);
setTimeout(()=>{
    console.log(2)
})
process.nextTick(()=>{
    console.log(3)
})
setImmediate(()=>{
    console.log(4)
})
new Promise((resolve,reject)=>{
    console.log(5);
    resolve();
    console.log(6)
}).then(()=>{
    console.log(7)
})
//代码从上往下往下执行：
// 1,5,6 同步执行；然后查看nextTick,输出3；然后查看微任务列表，输出7；输出7
//然后timmer 阶段：输出2，无nextTick 无微任务
// 然后io阶段，无
//然后check阶段：Immediate执行输出 4；
// 然后close阶段，无

```



8.js的垃圾回收处理机制的原理？（马蜂窝，美团）

(垃圾处理机制)[https://www.cnblogs.com/guoyongfeng/p/3907994.html]
```$javascript
不再用到的内存（一个不用到的值）没有及时释放，就叫做内存泄漏（memory leak）。

垃圾回收机制：引用计数
最常使用的方法叫做"引用计数"（reference counting）：语言引擎有一张"引用表"，
保存了内存里面所有的资源（通常是各种值）的引用次数。如果一个值的引用次数是0，就
表示这个值不再用到了，因此可以将这块内存释放

JavaScript的解释器可以检测到何时程序不再使用一个对象了，当他确定了一个对象是无用的时候，
他就知道不再需要这个对象，可以把它所占用的内存释放掉了



var arr = [1,2,3,4]
数组[1, 2, 3, 4]是一个值，会占用内存。变量arr是仅有的对这个值的引用，因此引用次数
为1。尽管后面的代码没有用到arr，它还是会持续占用内存;就会造成内存泄漏

arr = null;
如果增加一行代码，解除arr对[1, 2, 3, 4]引用，这块内存就可以被垃圾回收机制释放了。


垃圾回收机制:标记清除：
js中最常用的垃圾回收方式就是标记清除。
当变量进入环境时，例如，在一个函数中声明一个变量，就将这个变量标记为"进入环境"，
从逻辑上讲，永远不能释放进入环境变量所占用的内存，因为只要执行流进入相应的环境，
就可能会用到它们。而当变量离开环境时，则将其标记为"离开环境"




```



9.localstorage、sessionStorage 、cookie区别？（水滴）

二级域名是否可以取到一级域名的数据？
cookie是可以的 ，只要在一个主域即可
localstorage不可以，他是跨站点的，只要域名变了就取不到了。


10.什么是闭包，说下项目中应用场景？（水滴）
```$javascript
  闭包就是能够读取其他函数内部变量的函数，或者子函数在外调用，子函数所在的父函数的作用域不会被释放
   一个闭包就是当一个函数返回时，一个没有释放资源的栈区。
   
   场景：
   1.可以实现公有变量，函数外或在其他函数中访问某一函数内部的参数
    function add() {
             var num = 0;
             function demo(){
                num++;
                console.log(num);
                }
                return demo;
           }
    var test = add();
    test();//1
    test();//2
   
  2.为节点循环绑定click事件，在事件函数中使用当次循环的值或节点，而不是最后一次循环的值或节点
  
  3.计数器
    function checkCount(target){
        let count = 0;
        let callbacks = [];
        return function(fn){
              count++;
              if(count == target){
                 callbacks.forEach(fn=>fn())
              }else{
                 callbacks.push(fn)
              }
        }
    }
    
    let d = checkCount(3);
    d(fn);
    d(fn);
    d(n)
    
    4.函数节流和防抖
    
```


11.ajax工作原理？

```$javascript


var xhr = new XMLHttpRequest();
xhr.open('GET','/api',true);//true表示异步
xhr.onreadystatechange = function() {
  if(xhr.readyState == 4 && xhr.status == 200){
      console.log(xhr.responseText)
  }
}
xhr.send();


步骤：1.创建xhr请求对象
     2.创建http请求
     3.监听状态变化
     4.根据状态码处理结果,
            ⑴未初始化状态。在创建完XMLHttpRequest对象时，该对象处于未初始化状态，此时XMLHttpRequest对象的readyState属性值为0。
     
            ⑵初始化状态。在创建完XMLHttpRequest对象后使用open()方法创建了HTTP请求时，该对象处于初始化状态。此时XMLHttpRequest对象的readyState属性值为1。
     
            ⑶发送数据状态。在初始化XMLHttpRequest对象后，使用send()方法发送数据时，该对象处于发送数据状态，此时XMLHttpRequest对象的readyState属性值为2。
     
            ⑷接收数据状态。Web服务器接收完数据并进行处理完毕之后，向客户端传送返回的结果。此时，XMLHttpRequest对象处于接收数据状态，XMLHttpRequest对象的readyState属性值为3。
     
            ⑸完成状态。XMLHttpRequest对象接收数据完毕后，进入完成状态，此时XMLHttpRequest对象的readyState属性值为4。此时接收完毕后的数据存入在客户端计算机的内存中，可以使用responseText属性或responseXml属性来获取数据。
     
      只有在XMLHttpRequest对象完成了以上5个步骤之后，才可以获取从服务器端返回的数据。因此，如果要获得从服务器端返回的数据，就必须要先判断XMLHttpRequest对象的状态
     5.发送请求


```

12.js事件代理？（传智播客，58，瓜子二手车）
```$javascript

事件委托指的是，不在事件的发生地（直接dom）上设置监听函数，而是在其父元素上设置监听函数，通过事件冒泡，父元素可以监听到子元素上事件的触发，通过判断事件发生元素DOM的类型，来做出不同的响应。

举例：最经典的就是ul和li标签的事件监听，比如我们在添加事件时候，采用事件委托机制，不会在li标签上直接添加，而是在ul父元素上添加。


好处：比较合适动态元素的绑定，新添加的子元素也会有监听函数，也可以有事件触发机制。

jquery:
$("#nav").on('click',function(e){
    $(e.target) =>是每个点击的项目
    $(this) =>nav

})
$('#nav').on('click','li',function(){
   $(this) =>点击的每一项

});


扩展 jsdom事件流阶段：
   捕获阶段->目标阶段->冒泡阶段
   外内   ->  当前 ->冒泡
   
   如何执行冒泡 在捕获？
   在捕获阶段的函数暂缓执行，等冒泡执行完后在执行捕获函数。

```



13.js中this是如何工作的（基本都问）

14.怎么判断引用数据类型？（为什么能用Object.prototype.toString.call()去判断？）（水滴）
   
```$javascript
1.instanceof 判断不准确
var a = [];
a instanceof Array  true
a instanceof Object  true 

2.Object.prototype.toString.call([])  '[object Array]'
  Object.prototype.toString.call({})   "[object Object]"
  
  
3.isPrototypeOf
Array.prototype.isPrototypeOf([]) true
Object.prototype.isPrototypeOf([]) true
 
 isPrototypeOf 与instanceOf一样 都是查找原型链上是否存在。

4.[].constructor == Array.prototype.constructor



扩展：
Array.toString == Function.prototype.toString  true，
根据原型链查找，查到Function.prototype.toString就停了。不会查到Object.prototype

[].toString == Array.prototype.toString  true.
根据原型链查找，查到Array.prototype.toString就停了。不会查到Object.prototype

Array,Function 都在各自的原型上重新定义了toString方法，阻断了查找。





```


15.js变量提升和作用域查找？
```$javascript
console.log(type a) //undefined  变量提升
console.log(type b) //err:b is not defined ,后续代码不执行
console.log(type c) 
var a = function(){return true}; //变量提升
window.b = function(){  //给window创建属性，不存在变量提升
	return true;
}
console.log(a() && b() && c())
function c(){return c}


var o = {
	a:1,
	f(){
		return this.a;
	}
}
console.log(o.f()); //1
var o1 = o;
console.log(o1.f());//1
var o2 = o.f;
console.log(o2()); //undefined  =>this是window，window.a undefined;
o.a=  5;
console.log(o1.f());//5  堆内存 和 栈内存
var o3 = {a:5}; 
console.log(o.f.call(o3)) //5 
```

16.call apply bind 区别？如何实现？（基本都问）

17.实现跨域的方法？说一下JSOP的原理？（水滴）
（nginx服务器反向代理)[https://segmentfault.com/a/1190000012859206]
(postMessage + iframe实现页面跨域)[http://www.cnblogs.com/zhouzme/p/5758386.html]
```$javascript
jsonp;
   利用script标签src属性不受同源策略影响，可实现跨域；
   通过与api方，对接好 回调参数字段～ cb=fn,fn在前端是一个全局的方法。后端返回script代码"fn({name:1})",浏览器会执行；
     前端代码：
     function b(data) {
           console.log(data)
       }
       let script = document.createElement('script')
       script.src = "http://localhost:3002/jsonp?cb=b";
       document.body.appendChild(script)
     node模拟后端：
     
     http.createServer((req, res) => {
         let {pathname, query} = url.parse(req.url, true);
         if (pathname == '/jsonp') {
            let school = JSON.stringify({name:'zfpx'});
            // res.end(`var a  = ${school}`) //在全局就可以获取到 变量a
            //  res.end(`b(${school})`)
             res.end(`${query.cb}(${school})`)
             return ;
         }
     }).listen(port, () => {
         console.log(`${port} 服务器启动`)
     });
CORS
   服务端响应：
       res.setHeader('Access-Control-Allow-Origin',"*");//设置允许访问的源
       res.setHeader('Access-Control-Allow-Methods','GET,POST,PUT,DELETE,OPTIONS')
       res.setHeader('Access-Control-Allow-headers','name')
nginx服务器反向代理

   //前端a.com 请求 后端api b.com/login 会出现跨域，
   //在a.com所在服务器启动一个nginx代理服务器，监听a.com 的80端口；
   //配置代理规则  ^/apis/ => b.com/ ,
   //这让我们在请求a.com/apis/login 的时候，被nginx拦截，代理到 b.com/login
      
    


处理页面之间的跨域

window.name+iframe 需要目标服务器响应window.name。

window.location.hash+iframe 同样需要目标服务器作处理。

html5的 postMessage+ifrme 这个也是需要目标服务器或者说是目标页面写一个postMessage，主要侧重于前端通讯。


a.com/index.html 

 <iframe src="b.com/index.html" id="a">
 var data= {name:1}
 document.getElementById("a").contentWindow.postMessage(data, '*');
 window.addEventListener('message',function(data){
     
 }) 
 
b.com/index.html
注册监听消息事件：
window.addEventListener('message',function(data){
    
}) 
//向父页面post消息
window.parent.postMessage({ 
            
 }, '*');
   
   
   
```
18.正则相关（小米,boss，头条）
```javascript
a.正则捕获规则
b.如果是嵌套捕获，得到的顺序是什么样的。
c.正则捕获的引用和反向引用是什么
d.正则的匹配默认是贪婪匹配么

```

19.[].toString()为啥会返回逗号隔开的字符串


20.数据proxy劫持与defineProperty对比(阿里)。
[defineProperty 和 proxy 对比](https://blog.csdn.net/qq_42833001/article/details/83302149)
```$javascript
es5
defineProperty:修改或者定义一个对象的新属性， 返回这个对象

1.不能监听数组变化 
push, pop, shift, unshift,splice, sort, reverse，这些能修改原数组的方法成为变异方法；
vue的做法是把这些方法重写来实现数组的劫持；

2.必须遍历对象的每个属性，单独设置；
Object.keys(obj).forEach(key => {
  Object.defineProperty(obj, key, {
    // ...
  })
})

3.必须深层遍历嵌套对象
vue的深度劫持；

知识：
基础描述符
configrable:false,默认false,表示该属性是否可以修改 描述符配置；
enumerable:false,默认false，表示该属性是否可以枚举

设置和获取描述符
set(newval){}
get(){}

赋值描述符
value:'',
writable:false,表示值是否可被修改，默认false

设置和获取描述符和赋值描述符，只能有一个出现，基础描述符可以一直存在。
4.vue3.0跟2.0的区别说一下？

============================
es6
proxy 代理，将一个对象进行包装，从而可以代理 操作对象的一些行为；

1.针对对象:针对整个对象,而不是对象的某个属性; 不用 遍历属性；

let obj = {
  name: 'Eason',
  age: 30
}
let handler = {
  get (target, key, receiver) {
    console.log('get', key)
    return Reflect.get(target, key, receiver)
  },
  set (target, key, value, receiver) {
    console.log('set', key, value)
    return Reflect.set(target, key, value, receiver)
  }
}
let proxy = new Proxy(obj, handler)
proxy.name = 'Zoe' // set name Zoe
proxy.age = 18 // set age 18

// Reflect.get 和 Reflect.set 可以理解为类继承里的 super，即调用原来的方法
Reflect.get():获取对象身上某个属性的值，类似于 target[name]。
Reflect.set():将值分配给属性的函数,返回一个Boolean，如果更新成功，则返回true。

2.支持数组:不需要对数组的方法进行重载，省去了众多 hack
let arr = [1,2,3]
let proxy = new Proxy(arr, {
    get (target, key, receiver) {
        console.log('get', key)
        return Reflect.get(target, key, receiver)
    },
    set (target, key, value, receiver) {
        console.log('set', key, value)
        return Reflect.set(target, key, value, receiver)
    }
})
proxy.push(4)
// 能够打印出很多内容
// get push     (寻找 proxy.push 方法)
// get length   (获取当前的 length)
// set 3 4      (设置 proxy[3] = 4)
// set length 4 (设置 proxy.length = 4)



3.嵌套支持: get 里面递归调用 Proxy 并返回

let obj = {
  info: {
    name: 'eason',
    blogs: ['webpack', 'babel', 'cache']
  }
}
let handler = {
  get (target, key, receiver) {
    console.log('get', key)
    // 递归创建并返回
    if (typeof target[key] === 'object' && target[key] !== null) {
      return new Proxy(target[key], handler)
    }
    return Reflect.get(target, key, receiver)
  },
  set (target, key, value, receiver) {
    console.log('set', key, value)
    return Reflect.set(target, key, value, receiver)
  }
}
let proxy = new Proxy(obj, handler)
// 以下两句都能够进入 set
proxy.info.name = 'Zoe'
proxy.info.blogs.push('proxy')


```

21.js为什么要有protoType？如果没有会怎么样？(阿里)

22.generator函数？跟async有什么区别？（阿里）

```$javascript
generator函数？跟async有什么区别？
  async函数实际上是通过gnnerator函数实现的,实际上就是将一个generator函数自动执行的结果；
  generator 需要配合 co这样的库，才方便调用；
  async 是结合了co和generator的思想，实现的 ；
  
  async function fn(){
        let content1 = await ajax('1');
        let content2 = await ajax('2');
        return content2;
        
  }
  fn().then(res=>{
  
    //可以获取到content2
    
  })
  
  
  实际上实现的原理：
  
  function co(g){
    let ite = g();
    return new Promise((resolve,reject)=>{
        function next(data){
            let {value,done} = ite.next(data);
            if(!done){
                if(value instanceOf Promise){
                    value.then(res=>{
                        next(res);
                    },reject)
                }else{
                    next(value)
                }
            }else{
                resolve(value)
            }
        }
        next()
    })
  }
  
  
  function fn(){
  
    return co( 
            function *(){
               let content1 = yield ajax('1');
               let content2 = yield ajax('2');
               return content2;
               
         })
  }
  
`
  
  
  
  
  





23.
mouseover和mouseenter的区别


```$javascript

mouseover：当鼠标移入元素或其子元素都会触发事件，所以有一个重复触发，冒泡的过程。对应的移除事件是mouseout


mouseenter：当鼠标移除元素本身（不包含元素的子元素）会触发事件，也就是不会冒泡，对应的移除事件是mouseleave
```






[阮一峰es6要点总结——Proxy](https://www.jianshu.com/p/81eb68ae5eb1)


# HTML+CSS相关

1.meta标签常用有哪些，列举？viewport属性有哪些分别代表什么（水滴）
```$javascript


忽略页面中数字识别为电话 忽略email识别
<meta name="apple-mobile-web-app-status-bar-style" content="black"/>
关键字
<meta name="keywords" content="楚楚推，楚楚推平台">
网页描述
<meta name="description" content="楚楚推，分享生活，分享爱。">

允许加载哪个域的资源，防止xss攻击
<meta http-equiv="Content-Security-Policy">
 
 viewport虚拟窗口
 width：控制 viewport 的大小，可以指定的一个值，如果 600，或者特殊的值，如 device-width 为设备的宽度（单位为缩放为 100% 时的 CSS 的像素）。
 height：和 width 相对应，指定高度。
 initial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例。
 maximum-scale：允许用户缩放到的最大比例。
 minimum-scale：允许用户缩放到的最小比例。
 user-scalable：用户是否可以手动缩放
<meta name="viewport"content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no" /> 
 
 

```

2.清除浮动方法？（水滴）
  ```$javascript
a.添加空div，增加clear：both属性
  
  b.给父元素定义overflow：hidden，氟元素必须设置width
  
  c:给父元素增加伪元素 
        .clearfix:after {
                clear: both;
                content: '';
                font-size: 0;
                display: block;
                visibility: hidden;
                height: 0;
        }

    
```

  
3.口述css盒子模型（水滴）

```$javascript
content-box:
    可以使设置的宽度和高度值应用到元素的内容框，盒子的width只包含内容。即总宽度=margin+border+padding+width
border-box:
 设置的width值其实是除margin外的border+padding+element的总宽度，盒子的width包含border+padding+内容；即总宽度=margin+width
```

 
 
4.实现左右固定宽度中间自适应布局有哪几种？（水滴）

  a. 最外层元素 {padding:0 200px};
     所有子元素都向左浮动
     第一个子元素：width：100%；float：left
     第二个子元素：width：200px;float:left;margin-left:-100%;position:relative;left:-200px;
     第三个子元素：width：200px;float:left;margin-left:-200px;position:relative;left:200px;
     
  b.
     前两个子元素浮动，第三个子元素通过margin来控制宽度；
        <div>
        <div style="width: 200px;height: 200px;float: left;background: red"></div>
        <div style="width: 200px;height: 200px;float: right;background: green"></div>
        <div style="height:200px;margin:0 200px;background: blueviolet"></div>
        </div>
  c.
    flex布局；
  <div style="display: flex;">
        <div style="width: 200px;height: 200px;background: red"></div>
        <div style="flex: 1;height: 200px;background: yellow"></div>
        <div style="width: 200px;height: 200px;background: green"></div>
    </div>


5.position属性有哪几种，区别？（水滴）
  static、relative、absolute、fixed。

6.左侧固定宽度，右侧自适应？ 多种。
```$javascript
  a.左侧设置固定宽并且左浮动，右侧加个margin-left;
  .box1{
  	background: red;
  	width: 200px;
  	float: left;
  }
  .box2{
  	background: darkcyan;
    margin-left: 200px;
  }
  
  b.calc 计算右侧宽度,右浮动
  .box1{
    background:red;
    width:200px;
    float:left;
  }
  .box2{
    background:green;
    width:cacl(100% - 200px)
    float:right;
  }
  
  c.flex布局
  
  .outer{
    display:flex;
    flex-direction:row;  
  }
  .box1{
    width:200px;
    background:red
  }
  .box2{
    flex:1;
    background:green;
  }
  
  d.左侧设置固定宽并且左浮动，右侧加overflow：hidden；
  
   .box1{
      background:red;
      width:200px;
      float:left;
    }
    .box2{
      background:green;
      overflow:hidden;
    }
   
   e.absolute+margin
     左侧设置固定宽并且绝对定位，右侧加margin-left；
     .box1{
           background:red;
           width:200px;
           position:absolute;
           left:0;
         }
         .box2{
           background:green;
           margin-left:200px;
         }
     
     
  
```

7.用css实现一个三角形，为什么？
```$javascript
.triangle {
            width : 0;
            height: 0;
            border : 100px solid transparent;
            border-top : 100px solid blue; /*这里可以设置border的top、bottom、left、right四个方向的三角*/
}


```

8.div垂直水平居中，多种方式（宽高不限）

```$javascript
.box1{
            position: absolute;
            top: 50%;
            left: 50%;
            margin-left: -100px;
            margin-top: -100px;
        }
        .box1{
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%,-50%);
        }
        .box1{
            position: absolute;
            top:0;
            left: 0;
            right: 0;
            bottom: 0;
            margin: auto;

        }
        
        .parent{
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100%;
        }
        
```



9.img i iframe video canvas q ol nav form em dl dt dd br hr audio abbr span
strong sub textarea  那些是块？


10.块元素 和行内元素有哪些?
```$javascript
常用的 
    div ul li  p h1 
    
    span i img input  a em  textarea


快元素：独占一行；自上至下；可设置狂傲
行内元素：不独占一行；自左至右；设置宽高无效；设置margin padding，上下方向无效


可继承：font相关 font-size family weight font-style
      line-height
      text-align
      text-indent 文本缩进
      color 字体颜色
      word-spacing字间隔

```


11.css高度宽度分别为视窗的20%的盒子
```$javascript
 第一种:width:20%;padding-bottom:20%;
 第二种：width:20vw;height:20vw


```

 


# 前端框架相关

1.什么是mvvm？mvvm实现原理？

```
双向数据绑定
M 数据层
V 视图层
VM View-Modle层，主要用于数据和视图的通讯

利用Object.definedProperty 对data属性的get 和set 进行拦截和监听，并对每个data属性创建一个订阅池。
在编译dom阶段，对dom进行解析，"v-**" "{{}}"，每编译到一处， 完成首次渲染，而且注册一个监听者，添加到data属性的订阅池中。
数据影响视图：当数据改变，则通知该属性对应订阅池中所有的watcher，进行更新。
视图影响数据(v-model): 通过绑定事件，然后改变data属性值，data属性值发生改变，通知所有watcher更新视图。

设计思想：发布订阅模式，es6 definedProperty拦截。

某一个属性，具备多个watcher，存入到一个dep中。当属性变化，才会去执行dep中的watcher。是由被监听属性（目标属性所触发），所以属于观察者模式。




```

2.vue兄弟组件传递数据？
```$javascript
父传子：通过给子组件绑定属性传值，:data=""。子组件接收通过props设置接收的属性。
子传父：通过事件传值，给子组件绑定事件，@事件名="父组件方法",子组件改变了数据，
通过this.$emit(事件名,data)通知父组件。

兄弟组件传值:中间件
1.
创建一个单独的vm实例。
通过vm.$on('事件名',方法)绑定事件
通过vm.$emit(事件名，data)通知兄弟组件修改数据。
2.合并到同一个父组件下，通过父组件进行传值。
3.vuex

```
3.vuex的使用场景？如何使用？原理？（阿里）
```$javascript
应用场景:非父子关系的组件通信及共享数据，例如兄弟组件、祖孙组件。
当你打算开发大型单页应用（SPA），会出现多个视图组件依赖同一个状态，来自不同视图的行为需要变更同一个状态
 
1.组件销毁，仍想保留数据，再进渲染组件直接获取数据。
  举例：form表单弹窗组件，用户填写信息后，关闭弹窗。可在组件destroyed
  时将用户填写信息保存到state中。在created时，读取state中数据进行渲染
  
 
2.有些组件构建和加载是依赖异步数据。v-if="{{store.userInfo.vip}}"
 举例：有几个组件是与用户等级挂钩的，用户等级不同，展示的组件不同，并且这些组件
     都在不同的父组件中。
     可以将异步请求数据的方法和请求回来的数据保存到state中，集中管理调用。
     这样异步请求结束，所有的组件就都能得到状态，从而进行渲染。
    

3.多处共用数据，多处可更改数据，互相影响。
举例：购物车，详情页添加、删除，购物车页面添加删除
     消息列表消息数量
     订单相关跳转路由，订单列表跳转订单详情，订单详情跳转订单评价，
     不同路由之间订单数据共享。
 
============================================
如何使用：

============================================
原理:
1.全局注入store：
vuex 利用插件机制调用install进行安装vuex，install过程中利用mixin混入，全局注册混入对象，将会影响所有之后创建的Vue实例。
混入 beforeCreate 钩子，每个实例生成都会先调用混入的beforeCreate钩子（vuexInit），vueInit检查当前options中是否有store对象，
如果没有则会去父组件中查找。

a. Vue.use(Vuex) =>安装Vuex插件,调用Vuex的install方法。

b. Vuex.install =  function(){
    Vue.mixin({ beforeCreate: vuexInit });
}
store注入 vue的实例组件的方式，是通过vue的 mixin机制，借助vue组件的生命周期钩子beforeCreate 完成的。
即 每个vue组件实例化过程中，会在 beforeCreate 钩子前调用 vuexInit 方法。

c.vuexInit核心代码如下：
this.$store = typeof options.store === 'function'
    ? options.store()
    : options.store
    
this的指向，得益于mixin机制，this将指向 vue组件实例！最终，我们可以再vue组件实例上获得vuex的store 
对象的引用 $store！

源码：
function applyMixin (Vue) {
  var version = Number(Vue.version.split('.')[0]);

  if (version >= 2) {
    Vue.mixin({ beforeCreate: vuexInit });
  } else {
    var _init = Vue.prototype._init;
    Vue.prototype._init = function (options) {
      if ( options === void 0 ) options = {};

      options.init = options.init
        ? [vuexInit].concat(options.init)
        : vuexInit;
      _init.call(this, options);
    };
  }

  function vuexInit () {
    var options = this.$options;
    if (options.store) {
      this.$store = typeof options.store === 'function'
        ? options.store()
        : options.store;
    } else if (options.parent && options.parent.$store) {
      this.$store = options.parent.$store;
    }
  }
}

2.数据响应式，state 和 getter数据更新如何映射到各个组件实例中自动更新，并update组件。
某一组件store更新时，如何通知其他组件进行数据更新，和UI更新！通过简单分析可知，问题的根本就是组件通信的问题！

原理：vuex的state是借助vue的响应式data实现的，getter是借助vue的computed属性实现的。


在 new Vuex.Store({state,getters})实例过程中，
Store的构造函数执行了这样一个函数 resetStoreVM(this, state); this->store实例，就是我们用到的this.$store
作用:
    初始化了一个vue实例_vm。
    将state注入到该实例中，由于vue的data是响应式的，所以$$state 是响应式的。当我们在更改this.$store.state.xxx时，基于vue的data的响应式机制，所有相关的state.xxx的值都会自动更新，ui自动更新。
    
    将store.getters作为computed属性注入到_vm上，然后将store.getter映射出一个新的对象，定义get拦截去获取_vm.xxx;
    

实际应用中,我们修改this.$store.state.xxx  => this.$store._vm.data.$$state => store._vm.$$state
get state () {
    return this._vm._data.$$state
  }
实际应用中，我们获取getter属性， this.$store.getters.xxx ,实际上取得的 this.$store._vm.xxx 取得是_vm的计算属性


 源码:
 function resetStoreVM (store, state, hot) {
    //处理getter
    store.getters = {};
    var wrappedGetters = store._wrappedGetters;
    var computed = {};
    forEachValue(wrappedGetters, function (fn, key) {
      computed[key] = function () { return fn(store); };
      Object.defineProperty(store.getters, key, {
        get: function () { return store._vm[key]; },
        enumerable: true // for local getters
      });
    });
    //创建vue实例，响应数据
    store._vm = new Vue({
      data: {
        $$state: state
      },
      computed: computed
    });
    }
  }
```

4.用vue-router实现导航切换 类似tab栏的那种；（一个tab有三个页，下面展示的内容怎么组件实现）

5.vue和react区别 

[区别](https://www.sohu.com/a/244192046_100180425)

6.vue 的watch中可以使用箭头函数吗

```$javascript
    不能使用,箭头函数在声明时就确定了this指向，指向的是父级作用域上下文。
    当函数执行时，里面的this并不是vue实例。

```
7.vue自定义组件的双向绑定怎么实现？

```$javascript
一个组件上的 v-model 默认会利用名为 value 的 prop 和名为 input 的事件;
<search-input v-model="text""></search-input >
等价于<search-input v-bind:value="searchText"
                   v-on:input="searchText = $event"></search-input >
                   
所以 在组件内部的input标签上，就不能用v-model了
为了让它正常工作，这个组件内的 <input> 必须：
将其 value 特性绑定到一个名叫 value 的 prop 上
在其 input 事件被触发时，将新的值通过自定义的 input 事件抛出

Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

8.mvc和mvvm的区别，手画一个原理流程图，类似view model viewmodel的一个流程交互图

9.v-if 和 v-show的区别
```$javascript
v-show指令是通过修改元素的displayCSS属性让其显示或者隐藏,首次渲染消耗大，适合频繁切换
v-if指令是直接销毁和重建DOM达到让元素显示和隐藏的效果，。具备懒惰性，不适合频繁切换，首次消耗小。

```

10.v-if跟 v-else，如果太多了你可以怎么优化，可以用什么设计模式

11.<keep-alive></keep-alive>的作用是什么?
```$javascript
<keep-alive></keep-alive> 
包裹动态组件时，会缓存不活动的组件实例,主要用于保留组件状态或避免重新渲染。
用来对组件进行缓存，从而节省性能，由于是一个抽象组件，所以在v页面渲染完毕后不会被渲染成一个DOM元素
大白话: 比如有一个列表和一个详情，那么用户就会经常执行打开详情=>返回列表=>打开详情…这样的话列表和详情都是
一个频率很高的页面，那么就可以对列表组件使用<keep-alive></keep-alive>进行缓存，这样用户每次返回列表的时候，
都能从缓存中快速渲染，而不是重新渲染.

使用了 keep-alive组件的两种状态 
actived  组件被激活时调用，
deactived 组件被移除时调用


```



12.mixin 全局混入

[vue官方混入](https://cn.vuejs.org/v2/guide/mixins.html)
```$javascript

混入规则： 
 混入内容值为对象的选项，例如 methods, components 和 directives，将被混合为同一个对象。两个对象键名冲突时，取组件对象的键值对。
 混入的是钩子函数时 created...等，同名钩子函数将混合为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。
 
 特点：mixin中的this指的 当前vue实例。
 
 全局混入： 每个vue实例都混入该对象，包括组件的实例。
 Vue.mixin({
    created:function(){
        this.$options.xxx
    }
 })
 new Vue({
    el:xxx
 })
 
 局部混入:只在当前实例混入
 var mixin = {
    data(){
        return {
            name:1
        }
    }
 }
 new Vue({
    mixins:[mixin]
 })

```

13.vue插件

```$javascript

插件通常会为 Vue 添加全局功能。插件的范围没有限制——一般有下面几种：
添加全局方法或者属性，如: vue-custom-element

添加全局资源：指令/过滤器/过渡等，如 vue-touch

通过全局 mixin 方法添加一些组件选项，如: vue-router

添加 Vue 实例方法，通过把它们添加到 Vue.prototype 上实现。

开发:
let myplugin = {}
 myplugin.install= （Vue,optiosn）=>{
    //增加全局方法或者属性，Vue类调用的
    Vue.globalMethod = ()=>{
    
    }
    //
    // 2. 添加全局资源，全局指令 全局过滤器等
      Vue.directive('my-directive', {
        bind (el, binding, vnode, oldVnode) {
          // 逻辑...
        }
        ...
      })
      
     // 3. 注入组件，混入对象等，比如vue-router vuex
        Vue.mixin({
          created: function () {
            // 逻辑...
          },
          conponents:{}
          ...
        })
      //4.添加实例方法
      Vue.prototype.$myMethod = function (methodOptions) {
          // 逻辑...
        }
      //
}

使用：Vue.use(myplugin);
use方法会自动调用myPlugin.install并且传入Vue这个类
```
 

14.Vue.extend 和 Vue.component区别

```$javascript

Vue.component
// 注册组件，传入一个扩展过的构造器
Vue.component('my-component', Vue.extend({ /* ... */ }))

// 注册组件，传入一个选项对象 (自动调用 Vue.extend)
Vue.component('my-component', { /* ... */ })

// 获取注册的组件 (始终返回构造器)
var MyComponent = Vue.component('my-component')

注册组件，不管是Vue.component() 或者 局部components{},都可 传入组件选项或者组件构造器；

=============================================
Vue.extend 
//使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。
// 是需要new的，或者通过 Vue.component注册组件，或者 注册局部组件 components:{构造器}

var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})

// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount('#mount-point')
new Profile({el:'#mount-point'})



```

15.vue插件机制 封装组件

```$javascript
只用于渲染页面的组件
使用：Vue.use(search)全局就会可以用 <search-input></search-input>
import _search from './search.vue'
search.install = function(Vue,options){
   let searchPlugin  = Vue.extend(_search)
   //1.通过注册全局组件的方式
   Vue.component('search-input',searchPlugin) 
   Vue.component('search-input',_search) 
   
   //2.通过Vue.mixin 全局混入的方式
   Vue.mixin({
        components:{
            'search-input':_search
            
        } 
   })
   Vue.mixin({
           components:{
               'search-input':searchPlugin 
           } 
      })
}；

可报漏api 可受控制的组件
使用：Vue.use(toast)   调用this.$toast.show()

import _toast from './toast.vue'
let $vm
toast.install = function(Vue,options){
    if(!$vm){
        let searchPlugin  = Vue.extend(_toast)
        let $vm = new searchPlugin({ 
            el:document.createElement("div");
        })
        document.body.appendChild($vm.$el)
    }
    1.toast内部method提供两个方法show 和hide；
    
    2.在此处扩展
    $vm.show = function(){
        
    }
    $vm.hide = function(){
    
    }
    $vm.changePropsData = function(title){
        //title 为组件内部生声明的props
        $vm.title = title;
    }
    
    Vue.mixin({
        created:funtion(){
            this.$vm = $vm;
        }
    })
}；


```
16.vue 在计算属性的时候，computed和用method里的方法计算有什么区别

```$javascript

computed 
计算属性会依赖于他使用的data中的属性，只要是依赖的属性值有改变，则自动重新调用一下计算属性；
如果他所依赖的这些属性值没有发生改变，那么计算属性的值是从缓存中来的，而不是重新编译，那么性能要高一些，
所以vue中尽可能使用computed替代watch。

method 是实时调用，实时计算；没有缓存

watch 是监听属性，当属性变化就会执行响应的回调函数；

```



17.vue中render，nextTick介绍一下？





18.component中data:function(){} 和data:{}区别？


```$javascript


类比与引用数据类型。如果不用function return 每个组件的data都是内存的同一个地址，那一个数据改变其他也改变了，这当然就不是我们想要的。用function return 其实就相当于申明了新的变量，相互独立，自然就不会有这样的问题；js在赋值object对象时，是直接一个相同的内存地址。所以为了每个组件的data独立，采用了这种方式。
如果不是组件的话，正常data的写法可以直接写一个对象，比如data：{ msg ： ' 下载 ' }，但由于组件是会在多个地方引用
的，JS中直接共享对象会造成引用传递，也就是说修改了msg后所有按钮的msg都会跟着修改，所以这里用function来每次返回一个
对象实例。

如果组件用 data:{}，那么多个调用组件的地方，将公用一个data，会互相影响。
为什么会公用一个data？
import 引入的模块，es module导入导出的都是内存地址，导出值导入互相影响


```

19.vue生命周期钩子？
beforeCreate  实例创建之前
created 实例创建完成
beforeMount 挂载前
mounted 挂载后
beforeUpdate 更新前
updated  更新后
beforeDestory 销毁前
destoryed 销毁后
 
 
 20.vue-routers的实现
    
    ```$javascript
    a.hash /#/  + onhashChange 事件
    
    改变hash，浏览器的请求不包括hash部分，不会刷新页面；通过hashchange事件监听路由变化
    
    b.history操作
      
      history.back() history.go() history.forward()，这些前进后退的操作会触发popstate事件；
      window.addEventListener('popstate'，fn）
      
      
      pushState，replaceState 并不会触发popstate事件，这两个方法应用于浏览器的历史记录栈，在当前已有的 back、forward、go 的基础之上，它们提供了对历史记录进行修改的功能。
      只是当它们执行修改时，虽然改变了当前的 URL，但浏览器不会立即向后端发送请求，也就不会刷新；
      但是这两个方法不会触发popstate事件，需要我们手动去修改pushstate方法，增加消息通知；
      
      
      //1.对方法进行加工,并且发布消息;
      function apo(type){
        var source = window.history[type];
        return  function(){
            var event = new Event(type);
            event.arguments = arguments;
            window.disptchEvent(event);
            return source.apply(this,arguments);
        }
      }
      window.history.pushState = aop('pushState');
      window.history.replaceState = aop('replaceState');
     
     window.addEventListener('pushState',function(){console.log(1)})
      
     window.addEventListener('replaceState',function(){console.log(2)})
      
      
    2.发布订阅模式，创建event bus来实现；拦截pushState 和replaceState ,发布消息；
    
    
    
    
    
    
    
      
    ```

 

# 前端工程化相关
1.webpack基本配置有哪些？(都问)

```$javascript

entry 
output
module(loader)
plugin
resolve(alias extentions)

webpack.dllPlugin();
webpack.dllReferencePlugin();
webpack.commonChunkPlugin();




```

2.vue-cli3脚手架的基本配置？


[vue-cli3 支持全局注册基础化组件](https://cn.vuejs.org/v2/guide/components-registration.html#%E5%9F%BA%E7%A1%80%E7%BB%84%E4%BB%B6%E7%9A%84%E8%87%AA%E5%8A%A8%E5%8C%96%E5%85%A8%E5%B1%80%E6%B3%A8%E5%86%8C)

3.webpack作用？

```
1、合并js文件，压缩代码；处理图片资源等静态资源，减少http请求；
2、css预处理，编译less/sass等浏览器不能直接识别的语言；
3、对文件进行hash，可以防止浏览器缓存。
4、一切皆模块，让模块更容易服用，避免重复加载或者加载不必要的模块，并且能够防止全局变量冲突。
```

4.你对脚手架有研究过么?（小米）

5.项目打包部署你都是怎么弄的？（小米）
```$javascript
jekins部署，具体环境是运维来搭建的。
```



# 应用场景开发相关

1.简述微信公众号授权过程？（每日优鲜）

2.客户端与前端的交互的原理 ，怎么交互？（瓜子二手车）

安卓:webview
1.js全局调用客户端方法：java向前端注入了全局对象ccjBridgeInstance，这个对象上面有一些客户端提供的方法 供前端调用；
                     url传参，native拦截；
                     
                     协议拦截，与客户端定义协议，通过动态向html插入 iframe，请求协议地址传带参数；native进行拦截
                     shareFriends://com.culiu.JiuKuaiJiu###分享标题###分享地址             
                    
2.客户端调用js:window上声明响应的回调地址，window.方法名_callback，提供给客户端调用；

ios:UIwebview wkwebview
UIwebview:
1.js全局调用客户端方法：java向前端注入了全局对象ccjBridgeInstance，这个对象上面有一些客户端提供的方法 供前端调用；
                    url传参，native拦截
                    
2.客户端调用js:window上声明响应的回调地址，window.方法名_callback，提供给客户端调用；

WKwebview:
js调用客户端：window.webkit.messageHandlers[方法名].possmessage(data)；去调用
            url传参，native拦截
客户端调用js:window上声明响应的回调地址，window.方法名_callback，提供给客户端调用；


回调方法遇到的问题：
连续调用相同的bridge，native只调用一次回调；
而且当时我们的回调地址都是直接赋给window的一个属性了，导致我们的回调一次次覆盖，只保留了最后一次；
解决方案：将回调收拢，每个bridge方法，对应一个回调函数池，提供给native的方法就是遍历这个回调函数池中的回调，依次执行；
        调用brigde时，就是向回调函数池中添加 函数；



#场景算法题

1.将aabbcccda 字符传替换成abcda？（将连续重复的字符去重）
```$javascript
将连续重复的字符去重,这种会有重复项，aabbcccdc=>字符传替换成abcdc
reduce拼接，
function switchStr(str) {
    let arr = str.split('');
    return arr.reduce((prev,next,index,origin)=>{
        if(prev.slice(prev.length-1) == next){
            return prev;
        }else{
            return prev + next;
        }
    },'')
}

function switchStr(str){
    return str.replace(/(.)(/1)+/,function(...args){
        retrun args[1]
    })
}

```

2.js实现字符串trim方法
```$javascript
原生：
var a = '  123  '
a.trim();

js实现：
String.prototype.trim = function(){
    return this.replace(/(^\s+|\s+$)/g,function(){
       return ""
    })
}

```

2.原生js实现字符串split方法？

```javascript


```


3.函数节流？防抖？有何区别？

[防抖,节流](https://juejin.im/post/5c87b54ce51d455f7943dddb)

```javascript
// 函数节流
function throttle(fn,delay) {
    let flag = true;
    return function() {
        if(!flag){
            return;
        }
        flag = false;
        setTimeout(()=>{
            fn.call(this,arguments);
            flag = true;
        },delay)
    }
}
function throttle(fn,delay) {
  let flag = true;
  return function(...args) {
      if(!flag){
          return;
      }
      flag = false;
      setTimeout(()=>{
          fn.call(this,args);
          flag = true;
      },500)
    
  }
    
    
}



//防抖

function debounce(fn) {
  let timer = null;
  return function() {
    clearTimeout(timer);
    timer = setTimeout(()=>{
        fn.call(this,arguments)
    },1000)
  }
  
  function debounce(fn) {
      let timer = null;
      return function(...args) {
        if(timer){
            clearTimeout(timer);
            timer = null;
        }
        timer = setTimeout(()=>{
            fn.call(this,args)
        },300) 
      }
  }
  
  //区分： 节流在一定时间内调用多次，总是执行第一次调用的，执行完成后，才能再次调用。
  //      防抖在一定时间内调用多次，后续的调用会覆盖前面的调用，执行的永远是最后一次调用的。
    
   
}


```

4.[a,b,c],写个方法将数组全排列 ，变成[abc,acb,bac,bca,cab,cba] （百度）
```$javascript
思路:每次都取出一个，然后将除去这一个的其他数进行全排列。然后在将取出的那个数拼接到开头
距离：取出a，然后b和c的全排列有两种：bc，cb； 这样就可以算出abc，acb；
    取出b，然后a和c的全排列有两种：ac，ca； 这样就可以算出bac，bca；
    取出c，然后a和b的全排列有两种：ab，ba； 这样就可以算出cab，cba；
    
    递归千万条，出口第一条：出口就是当数组为两项时，就可以自己写出 全排列。

// [a,b,c]=>[abc,acb,bac,bca,cab,cba]
function permute(ary){
    if(ary.length <= 2){
        if(ary.length == 2){
            return [ary.join(''),ary.reverse().join("")]
        }else{
            return [ary[0]]
        }
    }
    let result = [];
    ary.forEach((item,index)=>{
        let all = permute(ary.slice(0,index).concat(ary.slice(index+1)));
        all = all.map(val=>{
            return item+val+'';
        })
        result = result.concat(all);
    })
    return result
}

console.log(permute(["a","b","c"]));

字符串全排列 abc =>[abc,acb,bac,bca,cab,cba]
const anagrams = str => {
    if (str.length <= 2) return str.length === 2 ? [str, str[1] + str[0]] : [str];
    return str.split('').reduce((acc, letter, i) =>
        acc.concat(anagrams(str.slice(0, i) + str.slice(i + 1)).map(val => letter + val)), []);
};
console.log(anagrams('1234'));
数组全排列 [a,b,c]=>[[a,b,c],[a,c,b]...]
    function permute(arr) {
        if(arr.length <= 2){
            if(arr.length == 2){
                return [arr,[arr[1],arr[0]]];
            }else{
                return [arr];
            }
    
        }
        return arr.reduce((prev,next,i)=>{
            let pool= permute(arr.slice(0,i).concat(arr.slice(i+1)));
            pool = pool.map(item=>{
                item.unshift(next);
                return item;
            });
            return prev.concat(pool)
        },[])
    }
    console.log(permute([ 1, 2, 3]));

```
5.数组对象去重(vipkid)

```$javascript

//key 和 value都对比；
//[{a:11,b:12},{c:21,d:22},{a:12,b:11},{c:21,d:22},{a:1,e:{name:'1'}},{a:1,e:{name:'1'}}]
function fn(ary){
    let obj = {};
    for(let i = 0 ;i < ary.length;i++){
        let key = JSON.stringify(ary[i]);
        obj[key] = true;
    }
    return Object.keys(obj).map(item=>{
        return JSON.parse(item)
    })
}


//[{a:11,b:12},{c:21,d:22}],[{a:12,b:11},{c:21,d:22}]
//找出两个数组对象中 key和value都相同的对象。找出{c:21:d:22}

function fn(a1,a2){
    let o1 = {};
    let result = []
    for(let i = 0 ;i<a1.length;i++){
        let key = JSON.stringify(a1[i]);
        o1[key] = true;
    }
    for(let i = 0 ;i<a2.length;i++){
        let key = JSON.stringify(a2[i])
        if(o1[key]){
            result.push(a2[i])
        }
    }
    return result;
}


```

6.你所用到的排序方法

7.实现一个方法，能够把多重数组变成一个一维数组,flat方法。[1,[1,2],[3,4,5]]转换为[1,1,2,3,4,5] （vipkid）
```javascript
function flat(arr) {
    let result = [];
    for (let i=0;i<arr.length;i++){
        if(Object.prototype.toString.call(arr[i]) == '[object Array]'){
            result = result.concat(flat(arr[i]))
        }else{
            result.push(arr[i])
        }
    } 
    return result;
    
}



```

8.12345数组混排 (水滴)

```$javascript
利用随机数

function switchAry(ary){
    let result = [];
    let len = ary.length;
    for(let i = 0 ;i<len;i++){
       let j =  Math.floor(Math.random()*ary.length); 
       let cur = ary.splice(j,1)[0];
       result.push(cur);
    }
    return result;

}
function sort(ary){
    return ary.sort(function(){
        return Math.random() - 0.5 
    })

}


 // Math.floor(Math.random()*5)  0-4，
 // Math.ceil(Math.random()*5)   1-5
 // Math.round(Math.random()*5)   0-5

```






9.将字符串中重复出现的字符，去重；
```$javascript
   1.转成数组，去重，然后拼接；
    function uqinue(str){
        let ary = str.split("");
        return Array.from(new Set(ary)).join("")    
    }

   2.遍历
    function uqinue(str){
        let result = "";
        for(let i = 0;i<str.length;i++){
            if(result.indexOf(str[i])==-1){
            result = result + str[i]
            }
        }
        return result;
    }
   
   
```
10.abcaaaaaabcabcabcabbbabc 获取abc的重复次数 和索引

```javascript

// 方案1，利用abc进行拆分成数组，数组长度-1为出现次数，；然后计算索引
function get(str,t) {
    let ary = str.split('abc');
    let n  = ary.length - 1;
    let iAry = [];
    ary.reduce((prev,next,i,)=>{
        if(i ==0){
            iAry.push(prev+next.length);
            return prev + next.length;
        }
        if(i == ary.length-1){
            return;
        }
        iAry.push( prev+t.length+next.length);
        return prev+t.length+next.length;

    },0)
    return {n,iAry}
}

var a = 'abcaaaaaabcabcabcabbbabcb'
console.log(get(a, 'abc'));

//方案二：replace方法匹配，参数：[匹配到的整个串，分组，分组...，匹配到整个串的开始索引]
function get(str,t) {
    let reg = new RegExp(t,'g');
    let i = 0;
    let iAry = [];
    str.replace(reg,function (...args) {
        i++;
        iAry.push(args[1])
    })


return {i,iAry}
}

var a = 'abcaaaaaabcabcabcabbbabcb'
console.log(get(a, 'abc'));


```




11.Function instanceof Object （阿里，头条）
   Object instanceof Function
```$javascript
  Function instanceof Object 返回true。
  原理：Function 是一个类同时也是一个函数，是Function类的一个实例;
  Function instanceof Function 是true。
  Function.__proto__ == Function.prototype；
  而Function.prototype.__proto__ 是Object.prototype；
  
  
 Object instanceof Function 返回true；
 原理：Object是一个类也是一个函数，所以是Function的一个实例。
 


```

12.用二分查找递归方法，插入一个值并返回索引；有序数组[1,2,3,5,7,8],插入4，返回索引3；
```javascript
   
   //非递归,遍历找到第一个出现大于他的值，然后记住索引，向他前面插入进去。如果没找到，则直接push进去
  function insert(ary,n) {
      let index;
      for(var i = 0 ;i<ary.length;i++){
          if(ary[i]>n){
              index = i;
              break;
          }
      }
      if(i>=ary.length){
          ary.push(n)
          return i;
      }else{
          ary.splice(index,0,n)
          return index;
      }
      
  }
  
  console.log(insert([1, 2, 4], 3));
    function insert(arr,n) {
            var l = [];
            var r = [];
            for(let i = 0 ; i<arr.length;i++){
                if(arr[i]<n){
                    l.push(arr[i])
                }else{
                    r.push(arr[i])
                }
            }
            return {
                index:l.length,
                ary:l.concat([n]).concat(r)
            }
          
        }


//二分插入，递归。
//思路：二分，用左侧最后一项进行比较，如果比插值大，说明应该插入到左侧数组中；
//    递归,继续二分记性比较。
//递归出口：当数组中只有一项时，就可以进行比较，就知道插入到哪了。
       
function insert(ary,num) {
    if(ary.length == 1){
        if(ary[0]>num){
            ary.unshift(num);
        }else{
            ary.push(num);
        }
        return ary;
    }
    let m = Math.floor(ary.length/2);
    let l = ary.slice(0,m);
    let r = ary.slice(m);
    if(l[l.length-1]>num){
        return insert(l,num).concat(r)
    }else{
        return l.concat(insert(r,num))
    }

}
console.log(insert([1, 2, 34,48,68], 34));


   

```

13.计算出数组的最大差值 [12,5,11,7,33,9](程序设计)
```javascript
   function adjective(arr) {
    let max = Math.max(...arr);
    let min = Math.min(...arr);
    return max - min 
    
   }
   
   
   //假设法
   function adjective(arr) {
    let min = arr[0];
    let max = arr[0];
    for(let i = 1; i<arr.length;i++){
        if(arr[i]<=min){
            min = arr[i];
        }
        if(arr[i]>=max){
            max = arr[i]
        }
    }
     return max - min ;
   }
   
   //先排序
   function fn(arr) {
        arr.sort((a,b)=>a-b)
        return arr[arr.length-1] - arr[0];
   }

```


14.请写出检查元素是否在屏幕可视区域的关键代码；
```$javascript
图片懒加载的原理：
方案一：1.获取图片 距离 滑动内容盒子 顶部的offsetTop
      （需要给滑动内容盒子增加position:relative属性，使他成为父级参照物）
        offsetParent:父级参照物
        第一个具有position属性且非static的父级，没有的话，最终是body
        一般情况下页面中所有元素的父级参照物都是body;
        document.body.offsetParent =>null//body 的父级参照物是null）
       2.获取滚动窗口的scrollTop值。
       3.获取滚动窗口的clientHeight值。
       
       监听滚动事件：当clientHeight+scrollTop >=offsetTop时，则进入了窗口。
       
       
方案二:dom.getBoundingClientRect()是获取某个元素相对于视窗的位置集合;
      {top,left,right,bottom}，四个值都是距离左边或者顶部的距离。
      1.通过getBoundingclientRect().top,获取当前原理距离视窗顶部的距离
      2.获取可视窗的高度document.body.clientHeight。
      3.当元素距离视窗顶部距离 < 视窗高度，则出现在视窗内
     
```

15.将片段1 用正则表达式替换成片段2 （boss直聘）

```$javascript

片段1 <h1  class="h4"><span class="label">boss直聘</span><h1>

片段2 <h1  class=\"h4\"><span class=\"label\">boss直聘</span><h1>
    function replaceStr(str) {
        let result = str.replace(/"/g,function(...args) {
          return "/"+args[0]
        })  
    
        return result;
    
    }

```
16.计算"abcadadacvabc"中出现最多的字符？

```$javascript
//预设一个最大次数，利用对象存储次数 key是字符，value是次数；每次遍历都与最大次数进行比较；
function fn(str){
    let max;
    let maxNum = 0 ;
    let obj = {};
    let ary = str.split('');
    for(let i = 0 ; i<ary.length;i++){
        if(obj[ary[i]]){
            obj[ary[i]] =  obj[ary[i]] +1;
        }else{
            obj[ary[i]] = 1;
        }
        if( obj[ary[i]] > maxNum){
            maxNum = obj[ary[i]];
            max = ary[i]
        }
        
    }
    return `次数${maxNum} 字符${max}`
}



```
17.输入a=2 b=3 ,输出a=3,b=2

```$javascript

function fn(a,b){
    var temp = a;
    a = b;
    b = temp;
    return {
        a,
        b
    }
}

function fn(a,b){
    a = a+b;
    b = a-b;
    a = a-b;
    console.log(a,b)
}
function fn(a,b){
    a = a -b;
    b = a+b;
    a = b-a;
    return {a,b}
}
function fn(a,b){
  [b,a] = [a,b]
  return {a,b}
}


```
18.找出整形数组中乘积最大的三位数 [-10,7,29,30,5,-10,-70]

思路：先排序由大到小排序，取前三位的乘机 和 末尾两位 * 首位的乘机进行比较。 
   末尾两位可能是 负负得正，所以才要和前三位进行比较

```$javascript
function get1(ary){
    ary.sort((a,b)=>b-a);
    let sum = 1;
    for(let i = 0;i<3;i++){
        sum = sum * ary[i];
    }
    let sum2 = 1;
    if(ary.length>3){
        sum2 = ary[ary.length-2]*ary[ary.length-1]*ary[0]
    }
    if(sum>sum2){
        return ary.slice(0,3)
    }else{
        return ary.slice(0,1).concat(ary.slice(ary.length-2))
    }
}

```


19.假设有n级台阶，每次最多允许跨m步（m<=n）,那么有多少种跨越方式？
[该题解析](https://segmentfault.com/a/1190000010486342)
 
```$javascript
思路：采用自顶向下的思考方式
f(n,m) = f(n-1,m)+f(n-2,m)+...+f(n-m,m)
当m=2时，这就是一个斐波那契数列。
同时，对于n阶变态跳，即n=m时，用公式有以下特点：
f(n) = f(n-1)+f(n-2)+...+f(1);//①
f(n-1) = f(n-2)+f(n-3)+...+f(1);//②
①-② 即f(n) = 2f(n-1)，可以看出n阶变态跳的结果，实际是一个等比数列，也就是f(n) = 2^(n-1)

function fn(n,m){
    var count = 0;
    if(n<=0){
        return 1; 
    }
    if(n>=m){
        for(let i =1 ;i<=m;i++){
            count += fn(n-i,m)
        }
    }else{
        count +=fn(n,n)
    }
    return count;
 }
//首先根据规律，存储前m项结果，当n<m时，有f(n,m)=f(n,n)=f(n)=2^(n-1)
//接下来我们依次计算n=m+1时的结果并存入数组
//根据思路提示，第n项结果等于数组逆序前m项的和，我们截取数组迭代求和
//最后返回顶层的数据，即是f(n,m)
function f(n,m) {
    var arr = [1];
    for (var i=1; i<m;i++) {
        arr.push(Math.pow(2,i));
    }
    for (var j=m+1; j<=n; j++) {
        arr.push(arr.slice(-m).reduce((a,b) => a+b))
    }
    return arr.pop();
}



//简单递归
function fn(n){
    if(n==1)return 1;
    return fn(n-1) * 2
}



```
20.写一个函数

输入：{ a:{
         b:{
            c:{
                d:'h',
                j:'l',
                o:{
                   p:'q',
                   r:"s"
                }
                t:'u'
            }
         },
         v:{
            w:{
                x:{
                    y:'z'
                }
            }
         }
      }
      e:{
        f:{
           i:k
        },
        m:'n'
      }
}

输出结果，按照层数，把同一层的属性放在同一个子数组内；
[[a,e],[b,v,f,m],[c,w,i],[d,j,o,t,x],[p,r,y]]

```$javascript

思路:每递归一遍，脱一层。
function setAry(obj,result) {
    let keys = Object.keys(obj);
    if(keys.length == 0 ){
        return result;
    }
    result.push(keys);
    let next = {};
    for(let key in obj){
        if(typeof obj[key] == 'object'){
            Object.assign(next,obj[key])
        }
    }
    return setAry(next,result) 
}




```
21.有两个链表，如何判断两个链表有交叉？


# DOM相关

1.如何判断一个dom是否在视窗中


```$javascript
图片懒加载的原理：
方案一：1.获取图片 距离 滑动内容盒子 顶部的offsetTop
      （需要给滑动内容盒子增加position:relative属性，使他成为父级参照物）
        offsetParent:父级参照物
        第一个具有position属性且非static的父级，没有的话，最终是body
        一般情况下页面中所有元素的父级参照物都是body;
        document.body.offsetParent =>null//body 的父级参照物是null）
       2.获取滚动窗口的scrollTop值。
       3.获取滚动窗口的clientHeight值。
       
       监听滚动事件：当clientHeight+scrollTop >=offsetTop时，则进入了窗口。
       
       
方案二:dom.getBoundingClientRect()是获取某个元素相对于视窗的位置集合;
      {top,left,right,bottom}，四个值都是距离左边或者顶部的距离。
      1.通过getBoundingclientRect().top,获取当前原理距离视窗顶部的距离
      2.获取可视窗的高度document.body.clientHeight。
      3.当元素距离视窗顶部距离 < 视窗高度，则出现在视窗内
     
```


2.dom事件中 DOMContentLoaded和window.onload的触发实际是什么 ？图片加载完成后是一个什么情况？（苏宁）

```javascript

//DOMContentLoaded 事件是DOM元素全部加载完成之后就会触发，图片等其他信息加载之前调用
// 在Dom节点创建完成后执行，如果有多个定义，则触发后会依次执行。
// jquery:
$(function() {
    //do something
})
$(document).ready(function() {
  //do something
})
$().ready(function() {
  //do something 
  //jquery默认参数是document $()
})
// 以上三种方式都是jq提供的dom加载完成事件，都是基于DOMContentLoaded dom2级事件的。
document.addEventListener('DOMContentLoaded', function() {
     // 此方法为原生js DOM加载完成执行的事件
})

//windown.onload
// 加载完所有页面内容才会触发，包括所有内容,包括图片，flash等所有文件.如果页面的这些内容很多会让用户等待很 长时间.
//只能定义一个，如果有多个定义则只执行最后一个


// 举例
// 比如：页面中只有一个img标签，当img节点创建完后就会执行$(function(){})中的代码，
// 当img的src指定的图片完全加载完后才会触发window.onload事件。


```
3.原生js实现jq的after方法

```$javascript
思路：获取当前元素的氟元素节点 parentNode
     获取当前元素的下一个元素节点nextElementsibling（或者nextSilbling & nodeType==1，递归找出）
     parentNode.inserBefore(被插入元素,nextElementsibling)


```


# 性能优化相关？

1.列举你能想到的前端性能问题和安全问题

    ```$javascript
    
     安全问题：
        a.xss攻击,就是攻击者想尽一切办法将可以执行的代码注入到网页中。
         评论功能，写入脚本内容，入库了。 get参数后面拼接了key=脚本； 这种很被容易写进页面；
        
         防御：
         转义字符：对于用户的输入应该是永远不信任的。最普遍的做法就是转义输入输出的内容，对于引号、尖括号、斜杠进行转义
                 利用js-xss 将内容进行过滤，转义；
         
         csp:建立白名单，配置规则，高速浏览器哪些外部资源可以加载；
         通常可以通过两种方式来开启 CSP：
         设置 HTTP Header 中的 Content-Security-Policy
         设置 meta 标签的方式 <meta http-equiv="Content-Security-Policy">
         
            只允许加载本站资源
            Content-Security-Policy: default-src ‘self’
            只允许加载 HTTPS 协议图片
            Content-Security-Policy: img-src https://*
            允许加载任何来源框架
            Content-Security-Policy: child-src 'none'
       b.  CSRF攻击？跨站点请求伪造如何防范
           原理就是攻击者构造出一个后端请求地址，诱导用户点击或者通过某些途径自动发起请求。如果用户是在登录状态下的话，
           后端就以为是用户在操作，从而进行相应的逻辑。
           
           防御：请求时post附带验证信息，比如验证码或者 Token；
                get请求不对数据进行修改
                服务端验证referer
                
       c.点击劫持，攻击的网站通过 iframe 嵌套的方式嵌入自己的网页中，并将 iframe 设置为透明，在页面中透出一个按钮诱导用户点击    
           防御：当通过 iframe 的方式加载页面时，攻击者的网页直接不显示所有内容了。     
    
    ```
    
    ```$javascript
    
    前端性能优化：（都问）
  
      前端代码的压缩 合并，减少http请求，和文件大小
      图片处理设置最大边界，base64；雪碧图；
      
      少操作dom减少dom回流，或者创建文档碎片；
      缓存dom节点，减少查找次数；
      应用节流和防抖处理函数；比如说滚动发出请求；
      
      preload资源预加载属性；
      图片懒加载；
      静态资源放到cdn上；
      离线存储:配置manifest配置应用程序缓存：CACHE MANIFEST，NETWORK，FALLBACK
        优点//
        离线浏览 - 用户可在应用离线时使用它们
        速度 - 已缓存资源加载得更快
        减少服务器负载 - 浏览器将只从服务器下载更新过或更改过的资源。
        // 更新缓存 
        用户清空浏览器缓存
        manifest 文件修改，可以配置一个版本号，
        由程序来更新应用缓存
        // 其他 //
        站点离线存储的容量限制是5M
      浏览器缓存机制；
        楚楚推：图片max-age：24小时；
                js css文件max-age：15分钟；
                协商缓存；
           
      webpack性能优化：
      有哪些方式可以减少 Webpack 的打包时间？
        1.优化loader，通过exclude和include，优化loader的文件搜索范围；
      module.exports = {
        module: {
          rules: [
            {
              // js 文件才使用 babel
              test: /\.js$/,
              loader: 'babel-loader',
              // 只在 src 文件夹下查找
              include: [resolve('src')],
              // 不会去查找的路径
              exclude: /node_modules/
            }
          ]
        }
      }
      
      2.使用HappyPack插件；
      受限于 Node 是单线程运行的，所以 Webpack 在打包的过程中也是单线程的，特别是在执行 Loader 的时候，长时间编译的任务很多，这样就会导致等待的情况。
      HappyPack 可以将 Loader 的同步执行转换为并行的，这样就能充分利用系统资源来加快打包效率了
      module: {
        loaders: [
          {
            test: /\.js$/,
            include: [resolve('src')],
            exclude: /node_modules/,
            // id 后面的内容对应下面
            loader: 'happypack/loader?id=happybabel'
          }
        ]
      },
      plugins: [
        new HappyPack({
          id: 'happybabel',
          loaders: ['babel-loader?cacheDirectory'],
          // 开启 4 个线程
          threads: 4
        })
      ]
      3.webpack.DllPlugin + webpack.DllReferencePlugin
      DllPlugin 可以将特定的类库提前打包然后引入。这种方式可以极大的减少打包类库的次数，只有当类库更新版本
      才有需要重新打包，并且也实现了 将公共代码抽离成单独文件的优化方案。
      4.resolve.extensions：用来表明文件后缀列表，默认查找顺序是 ['.js', '.json']，如果你的导入文件没有添加后缀就会按照这个顺序查找文件。我们应该尽可能减少后缀列表长度，然后将出现频率高的后缀排在前面
      5.resolve.alias：可以通过别名的方式来映射一个路径，能让 Webpack 更快找到路径
      
      
      
      有哪些方式可以让 Webpack 打出来的包更小？
      1.按需加载，
      原理：当使用时候再去下载对应文件，返回一个promise；
      2.代码压缩；uglifyJS-webpack-plugin    webpack-parallel-uglify-plyugin 并行压缩js
      3.Tree Shaking可以实现删除项目中未被引用的代码，
       // test.js
       export const a = 1
       export const b = 2
       // index.js
       import { a } from './test.js'
      test 文件中的变量 b 如果没有在项目中使用到的话，就不会被打包到文件中
      
      4 Scope Hoisting Scope Hoisting 会分析出模块之间的依赖关系，尽可能的把打包出来的模块合并到一个函数中去
        module.exports = {
      optimization: {
        concatenateModules: true
      }
       Webpack 4 的话，开启生产环境就会自动启动这个优化功能。
    }
    
    
    ```
   
     


2.项目优化方法

3.错误检测上报日志怎么实现的?
   
  //a.通过onerror 捕获代码运行错误，捕获到错误信息后可以向目标服务器img发一个请求，get传递错误信息；
  window.onerror = function(msg, url, line, col, error){
  
  
  }
  //b.页面埋点，监听用户交互和pv/uv，请求服务器进行上报
  
  //c. performance.getEntriesByType('navigation') 针对一些复杂页面，获取页面的性能相关信息，进行上报；
  
 
4.fis3 和 webpack的区别是什么？
```$javascript
   a.入口，
   webpack是从entry出发，将依赖的文件提取编译打包，通过commonschunkplugin、dll等提取公共代码；
      fis3 是以file对象为中心构建编译的，所有文件同等对待，都会去分析文件依赖关系，生成一个静态资源表，
      资源表记录每个文件的依赖关系；
      fis3可以针对每个文件做出相应的配置，更加细致；
    
   
   b.fis3的配置文件，fis.match到某些文件，然后对这些文件定义各自的各个阶段的插件处理、发布规则,是针对文件进行匹配的;
    webpack配置文件，entry、output、plugins、module、resolve;
   
   c.fis3能对任何文件使用hash控制，在引用它的任何地方的路径会被自动替换为hash路径。
    
   d.fis3，由于独有的静态资源标记，导致npm生态不太好；
   

```
  


5.请列举最重要的前端优化点(按重要性)

6.：我们为什么一再强调将css放在头部，将js文件放在尾部

[浏览器解析](https://www.cnblogs.com/caizhenbo/p/6679478.html)
```$javascript
在面试的过程中，经常会有人在回答页面的优化中提到将js放到body标签底部，原因是因为浏览器生成Dom树的时候是一行一行读HTML代
码的，script标签放在最后面就不会影响前面的页面的渲染。那么问题来了，既然Dom树完全生成好后页面才能渲染出来，浏览器又必须读
完全部HTML才能生成完整的Dom树，script标签不放在body底部是不是也一样，因为dom树的生成需要整个文档解析完毕。
  
我们再来看一下chrome在页面渲染过程中的，绿色标志线是First Paint的时间。纳尼，为什么会出现firstpaint，页面的paint
不是在渲染树生成之后吗？其实现代浏览器为了更好的用户体验,渲染引擎将尝试尽快在屏幕上显示的内容。它不会等到所有HTML解析之前
开始构建和布局渲染树。部分的内容将被解析并显示。也就是说浏览器能够渲染不完整的dom树和cssom，尽快的减少白屏的时间。
假如我们将js放在header，js将阻塞解析dom，dom的内容会影响到First Paint，导致First Paint延后。所以说我们会将js放在
后面，以减少First Paint的时间，但是不会减少DOMContentLoaded被触发的时间。


```

7.vue首屏加载很慢，怎么进行优化？
```$javascript
a.组件异步加载;
   局部注册异步组件(`import` 函数会返回一个 `Promise` 对象。)
   new Vue({
     // ...
     components: {
       'my-component': () => import('./my-async-component')
     }
   })
   =>
   new Vue({
        // ...
        components: {
          'my-component': Promise.resolve({...组件定义对象})
        }
   })
   全局注册异步组件
   Vue.component("custom",() => import('./my-async-component'))
   =>Vue.component("custom",Promise.resolve({...组件定义对象}))
   或者
   
    Vue.component('async-example', function (resolve, reject) {
        resolve({
        template: '<div>I am async!</div>'
        })
    })
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 `require` 语法将会告诉 webpack
  // 自动将你的构建代码切割成多个包，这些包
  // 会通过 Ajax 请求加载
  require(['./my-async-component'], resolve)
})


  2.路由懒加载+分组打包；
  没有指定webpackChunkName，每个组件打包成一个js文件；
  指定了相同的webpackChunkName，会合并打包成一个js文件。
  const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
  const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
  const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
  const router = new VueRouter({
    routes: [
      { path: '/foo', 
        component: Foo，
        children:[
            {path:'/bar',component:Bar}
            {path:'/baz',component:Baz}
        ]
      }
    ]
  })
  
  
  3.组织复杂页面的代码时，可以考虑对于打开首屏时不需要渲染的子组件，使用v-if控制其只在需要的时候被渲染。
  
  4.cdn加载外部的css js文件，浏览器并发请求加载。
  
  html直接引入外部的js 和css文件，比如vue vuex
  
  利用webpack的externals忽略打包文件；key是导入的键，value是项目中用到的变量
   externals: {
      jquery: 'jQuery' ,
      vue:'Vue'
      //将需要忽略打包的都写在这个里面，但前提是index.html文件里面必须script引入
    }
    
   使用：import $ from 'jquery'，正常使用
   5.开启gzip压缩支持
   npm install compression-webpack-plugin —save-dev 
   将config/index.js 开启压缩
       productionGzip: true,
       productionGzipExtensions: ['js', 'css'],
   
   注：想要线上使用还需还得配置后端。
   
   
   扩展常用webpak插件
   copy-webpack-plugin ：复制文件到目标文件夹。在开发时使用热模替换，（没有生成dist 文件夹，都在内存中），如果想引用某一个js文件，直接写script标签是找不到的，因为服务器内存中没有这个文件。所以复制这个文件，到dist中。
   compression-webpack-plugin： 生产环境时可选择让代码压缩gzip.
   html-webpack-plugin : 生成index.html 并自动注入打包后的js css 等
   webpack.DefinePlugin： 可以生成配置常量。编译时就有的常量。
   extract-text-webpack-plugin： 提取使用文件的css 组成一个或多个css 文件。
   webpack.optimize.CommonsChunkPlugin： 让多个出口文件组成一个文件
   webpack-dev-server: 开发时使用，静态服务器，并且有热替换等功能。
   uglifyjs-webpack-plugin： 删除警告，压缩代码等
 
```
 

# 扩展知识？能聊就行～

1.简述CI/CD？

2.多态和重载？

```$javascript
重载：相同函数名，不同参数，则认为是两个不同的函数；
js没有函数重载，可以通过参数个数，参数类型，内部模仿重载；

多态的实际含义是，对象的多种形态；同一操作，作用在不同的对象上面，可以产生不同的解释和不同的执行结果。
javascript的变量类型在运行期是可变的，这就意味着javascript对象的多态性是与生俱来的。

```

3.了解服务端的反向代理吗？

4.订餐功能 如何实现的？
利用express框架搭建node服务，对前端提供api请求接口；
利用express-router + app.use 实现父子路由管理,实现用户订餐的增删改；
利用node-schedule 包实现定时任务，每天定时抓取用户订餐状态,生成订餐名单；每天定时清除，所有用户的 订餐状态；
利用nodemailer向行政发送 订餐人员名单;
let dinner = express.Router();
dinner.get('/list',function(){

})

dinner.post('/set',function(){

})

dinner.post('/delete',function(){})

app.use('/dinner',dinner)


表结构：
主键：是用户id ，用工号代替；姓名；部门；订饭状态；
用户进入前端页面，post提交工号，然后将该同学的订饭状态改成订餐状态；






# 设计模式

1.观察者模式 和 订阅发布模式的区别

![image](https://upload-images.jianshu.io/upload_images/5262488-291da39f66dbc28a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
```javascript
// 观察者模式和发布订阅模式最大的区别就是发布订阅模式有个事件调度中心。
// 观察者模式由具体目标调度，每个被订阅的目标里面都需要有对观察者的处理，会造成代码的冗余。而发布订阅模式则统一由调度中心处理，
// 消除了发布者和订阅者之间的依赖。

```




# 程序执行题

1.代码执行结果（boss直聘）
```javascript
var data = {
    name:'boss',
    age:3
}
console.log(data.age);//3
observe(data);
console.log(data.age);//4

data.age = 5; //val = 6
console.log(data.age)//7

function observe(data) {
    Object.keys(data).forEach(function (key) {
        defineReactive(data,key,data[key])
    })
}
function defineReactive(data,key,val) {
    Object.defineProperty(data,key,{
        enumerable:true,
        configurable:true,
        get(){
            return val +1;
        },
        set(newVal){
            val = newVal+1;
        }
    })
}


```
2.代码执行结果
```javascript
const arr = [1,2,3,4,5,6];
const  res = arr.filter(value  =>value%2).map(item=>item*item).reduce((a,b)=>a+b);
console.log(res);//35
console.log(arr.pop());//6
console.log(arr);//[1,2,3,4,5]


```
3.程序输出结果
```javascript
const arr = [1,2,3,4,5,6]
for(var i = 0;i<arr.length;i++){
    setTimeout(_=>{
        console.log(arr[i])
    },0)
}
//输出6个undefined


```

#node

1.express的设计思想？
路由控制
中间件
静态文件服务
模板解析

let app = express();
app.get('',(req,res,next)=>{

},(req,res,next)=>{}).post('',(req,res.next)=>{

})

app.use('',(req,res,next)=>{


})


let user =  express.Router();
user.use('/login',(req,res,next)=>{

})

user.get('',(req,res)=>{


})
app.use('/user',user)

设计思想
1.路由：二维数组的二维数据形式
[[cb,cb,cb],[cb,cb,cb],[cb,cb,cb],[usecb,usecb,usecb]]
最外层是Router，[cb,cb,cb] 表示的route，cb指的是每个route中的回调；[usecb,usecb,usecb]表示中间件的回调；

2.路由服务
express.router()，创建路由，在利用app.use中间件，形成父子级别中间路由；
express.router返回的一个router函数，router函数复用了app的一些方法,
也同样具备handler/post/get/use等方法；这样就利用方法复用，实现了一层一层的套用；


3.静态资源服务，调用node原生利用 http服务起的服务；



2.express和koa的区别？（阿里）

express 自带路由控制，express-router ;koa 需要单独引入
express 基于回调函数，koa是基于generator思想的；koa是不会出现回调地狱的；





3.node 思想？

特点：
线程是单线程异步。通过callback实现异步。
非阻塞、异步I/o，能用异步 绝不同步
event-driven事件驱动-发布订阅模式

思想：基于事件驱动，异步io，通过callback实现异步
     commonjs模块化，一个js就是一个模块；

自由回答题：
在一个异世界里，老虎可以吃羊，也可以吃草，并且羊比草好吃。但是吃了羊就会变成羊。假设现在有100只老虎和1只羊，且所有老虎都非常理性，那么最终会剩下多少老虎和多少羊？（头条）
     






