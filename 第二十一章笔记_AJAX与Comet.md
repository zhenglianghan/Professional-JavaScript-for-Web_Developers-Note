# 第二十一章 AJAX与Comet

1. Ajax是对Asynchronous JavaScript+XML（异步JavaScript与XML）的简写。这一技术能够向服务器请求额外的数据而无需卸载页面。

### XMLHttpRequest对象

1. 使用XMLHttpRequest对象发送Ajax的基本步骤：新建XMLHttpRequest对象、open()、send()：

   ```javascript
   var xhr = new XMLHttpRequest();
   xhr.open("GET","example.php");
   xhr.send();
   ```

   opne()方法还可以接收第三个参数，表示是否异步发送请求，默认为true，不推荐为false。

2. 在收到响应后，响应的数据会自动填充XHR对象的下列属性：

   - responseText : 作为响应主体被返回的文本
   - responseXML : 如果响应内容时"application/xml"或者"text/xml"，响应数据在此属性中
   - status : http状态码
   - statusText : http状态文本

3. XHR对象的readyState属性可能有下列值：

   - 0 : 未初始化
   - 1 : 已调用open()方法未调用send()方法
   - 2 : 已调用send()方法未收到响应
   - 3 : 已接收到部分数据
   - 4 : 已接收到全部数据，而且已经可以在客户端中使用了

   只要这个值发生变化，就会触发readyStateChange事件，于是我们就可以在这个事件中检测readyState的值来对响应的数据进行处理，注意，事件处理程序应该放在open()方法之前来提高兼容性：

   ```javascript
   var xhr = new XMLHttpRequest();
   xhr.onreadystatechange = function(){
       if(xhr.readyState == 4){
           if((xhr.status>=200&&xhr.status<300)||xhr.status==304){
               alert(xhr.responseText);
           }else{
               alert("unsuccessful " + xhr.status + xhr.statusText);
           }
       }
   }
   xhr.open("GET","example.php");
   xhr.send();
   ```

4. 另外，在接收到响应之前还可以调用**abort()**方法来取消异步请求，同时会对XHR对象进行解引用操作。

5. 使用setRequestHeader()方法可以设置自定义请求头部，但是必须在open()之后send()之前设置：

   ```javascript
   var xhr = new XMLHttpRequest();
   xhr.open("GET","example.php");
   xhr.setRequsetHeader("Origin","http://www.baidu.com");
   xhr.send();
   ```

6. 使用getResponseHeader()方法可以获得响应头特定属性的值，使用getAllResponseHeader()方法可以获得所有的响应头信息：

   ```javascript
   var origin = xhr.getResponseHeader("Origin");  //http://www.baidu.com
   var all = xhr.getAllResponseHeader();
   /*Connection: keep-alive
   Content-Length: 170
   Content-Security-Policy: default-src 'self'
   Content-Type: text/html; charset=utf-8
   Date: Wed, 17 Oct 2018 06:58:46 GMT
   X-Content-Type-Options: nosniff
   X-Powered-By: Express*/
   ```

7. GET请求把查询字符串放在url后面，注意一定要将查询字符串使用encodeURIComponent(value)方法将其编码，下面这个函数可以辅助向现有的URL末尾添加查询字符串参数：

   ```javascript
   function addURLPram(url,name,value){
       url += (url.indexOf(?) == -1 ? "?" : "&");
       url += encodeURIComponent(name) + "=" encodeURIComponent(value);
       return url;
   }
   ```

8. POST请求将请求数据传入到send()方法中。

### XMLHttpRequset 2 级

1. FormData对象为序列化表单以及创建与表单格式相同的数据（用于XHR传输）提供了便利。

   下面的代码将创建一个空的FormData对象:

   ```js
   var formData = new FormData(); // 当前为空
   ```

   你可以使用[`FormData.append()方法来添加键/值对到表单里面；

   ```js
   formData.append('username', 'Chris');
   ```

   或者你可以使用可选的`*form参数来创建一个带预置数据的FormData对象*`:

   ```js
   var formData1 = new FormData(document.forms[0]);
   formData1.append("name","Nico");
   var xhr = new XMLHttpRequest();
   xhr.open("POST","./resource/test.json");
   xhr.send(formData1);
   ```

   **注意**: 所有的输入元素都需要有**name**属性，否则无法访问到值。

2. XHR有一个timeout属性，表示请求在等待响应多少毫秒后就终止。超时时会触发ontimeout事件，使用示例：

   ```js
   var xhr = new XMLHttpRequest();
   xhr.open('GET', '/server', true);
   
   xhr.timeout = 2000; // 超时时间，单位是毫秒
   
   xhr.onload = function () {
     // 请求完成。在此进行处理。
   };
   
   xhr.ontimeout = function (e) {
     // XMLHttpRequest 超时。在此做某事。
   };
   
   xhr.send(null);
   ```

   **注意：在同步Ajax请求中设置此属性会抛出错误**

### 进度事件

1. 六个进度事件：
   - loadstart : 在接收到响应数据的第一个字节时触发。
   - progress : 在接收响应期间持续不断触发。
   - error : 在请求发生错误时触发。
   - load : 在接收到完整的响应数据时触发。
   - loadend : 在通信完成或者触发error或load事件后触发。

2. onload事件处理程序中的event.target指向XHR对象。

3. progress事件处理程序的事件对象有一个loaded属性表示已接收多少字节数据，total属性表示一共有多少字节数据,lengthComputable表示进度是否可用。可以在接收数据时显示进度：

   ```js
   var xhr = new XMLHttpRequest();
   xhr.onprogress = function(e){
       var statusDiv = document.getElementById("status");
       console.log(e);
       if(e.lengthComputable){
           statusDiv.innerHTML = "Received: " + e.loaded + " of " + e.total + "bytes";
       }
   }
   xhr.onload = function(e){
       if(xhr.status==200||xhr.status==304){
           console.log(xhr.responseText);
       }
   }
   xhr.open("GET","./resource/test.json");
   xhr.send(null);
   ```

### 跨域资源共享

1. **CORS**(Cross-Origin Resource Sharing, 跨域源资源共享)。一个简单的使用GET或POST发送的请求，它没有自定义的头部，而主体内容时text/plain。在发送该请求时，会有一个**Origin**头部，其中包含请求页面的源信息（协议、域名和端口），以便服务器根据这个头部信息来决定是否给予响应。如果服务器认为这个请求可以接受，就在**Access-Contrrol-Allow-Origin**头部中回发相同的源信息（如果是公共资源，可以回发"*"）。
2. 现代浏览器在尝试打开不同来源的资源时，只需要使用标准的XHR对象并在open()方法中传入绝对URL即可。跨域XHR对象有下面这些安全限制：
   - **不能使用setRequestHeader()方法设置自定义头部**。
   - 不能发送和接收cookie。
   - 调用getAllResponseHeader()方法总会返回空字符串。

### 其它跨域技术

1. 一个网页可以从任何网页中加载图像，不用担心跨域不跨域。这是在线广告跟踪浏览量的主要方式。新建一个图像类型，然后设置它的src属性，就可以向服务器发送请求：

   ```js
   var img = new Image();
   img.onload = img.onerror = function(){
       alert("Done");
   }
   img.src = "http://www.example.com?name=nico";
   ```

   图像ping有两个主要的缺点：一是只能使用GET请求，二是无法访问服务器返回的数据。

2. script标签也可以加载任何域中的资源。这诞生了非常常用的**JSONP**,JSON with padding(填充式JSON或参数式JSON)。使用示例：

   ```js
   var script = doucment.createElement("script");
   var head = document.getElementsByTagName("head")[0];
   function alertPrice(response){
       alert(response);
   }
   script.src = 'http://api.money.126.net/data/feed/0000001,1399001?callback=alertPrice';
   head.appendChild(script);
   ```

3. Comet是一种服务器向页面推送数据的技术。Comet能够让信息近乎实时地被推送到页面上，非常适合处理体育比赛的分数和股票报价。

4. 有两种实现Comet的方式。一种是长轮询，另一种是流。长轮询使用XHR对象和setTimeout()实现。流通过侦听readystatechange检测和检测readyState是否为3来实现：

   ```js
   function createStreamingClient(url, progress, finished){                   
       var xhr = new XMLHttpRequest(),
           received = 0;
   
       xhr.open("get", url, true);
       xhr.onreadystatechange = function(){
           var result;
   
           if (xhr.readyState == 3){
   
               //只取得最新数据并调整计数器
               result = xhr.responseText.substring(received);
               received += result.length;
   
               //调用progress回调函数
               progress(result);
   
           } else if (xhr.readyState == 4){
               finished(xhr.responseText);
           }
       };
       xhr.send(null);
       return xhr;
   }
   
   var client = createStreamingClient("streaming.php", function(data){
       alert("Received: " + data);
   }, function(data){
       alert("Done!");
   });
   ```

5. SSE API用于创建到服务器的单向连接，服务器可以通过这个连接来发送任意数量的数据。服务器相应的MIME类型必须是text/event-stream，用法：

   ```js
   var source = new EventSource("myevents.php");  
   source.onmessage = function(event){
       var data = event.data;
       //处理数据
   }
   ```

   构造函数传入的URL必须同源，EventSource的实例有一个readystate属性，0表示正在连接到服务器，1表示打开了连接，2表示关闭了连接，另外，还有三个事件,open在建立连接时触发，message:在从服务器接收到新事件时触发，error:在无法建立连接时触发。要强制关闭连接，调用source.close()方法。

6. Web Sockets可以在一个单独的持久连接上提供全双工、双向通信。在JavaScript中创建了Web Socket后，会有一个HTTP请求发送到服务器以发起连接，在取得服务器响应后，建立的连接会从HTTP协议交换为Web Socket协议。

7. Web Sockets未加密的连接时ws:// 加密的连接是wss://

8. 要创建Web Socket，先实例一个WebSocket对象并传入要连接的URL：

   ```js
   var socket = new WebScoket("ws://www.example.com/server.php");
   ```

   注意必须传入绝对URL，同源策略对Web Sockets不适用，是否会与某个域中的页面通信完全取决于服务器。

9. 与XHR类似，WebSocket也有一个表示当前状态的readyState属性，值不相同而已：

   - WebSocket.OPENING(0) : 正在建立连接
   - WebSocket.OPEN(1) : 已经建立连接
   - WebSocket.CLOSING(0) : 正在关闭连接
   - WebSocket.CLOSE(0) : 正在关闭连接

10. 发送数据使用socket.send()，关闭连接使用socket.close()

11. WebSocket对象的三个事件：

    - open : 成功建立连接时触发
    - error : 发生错误时触发
    - close : 连接关闭时触发。

12. CSRF(Cross-Site Request Forgery，跨站点请求伪造)。未被授权系统会伪装自己，让处理请求的服务器认为它是合法的。应对：

    - 要求以SSL连接来访问可以通过XHR请求的资源。
    - 要求每一次请求都要附带经过相应算法计算得到的验证码

    注意，下列措施对防范CSRF攻击不起作用。

    - 要求发送POST而不是GET请求------很容易改变
    - 检查来源URL以确定是否可信------来源记录很容易伪造
    - 基于cookie信息进行验证------同样很容易伪造













