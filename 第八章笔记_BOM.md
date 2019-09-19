# 第八章 BOM

### window对象

1. 全局变量不能通过delete删除（它的[[Configurable]]属性为false），而定义在window对象上的属性可以。

2. 尝试访问未声明的变量会抛出错误，但是**通过查询window对象，可以知道某个可能未声明的变量是否存在**。例如：

   ```javascript
   var newValue = oldValue;  //抛出错误,oldValue未定义
   var newValue = window.oldValue;  //不抛出错误，因为这是一次属性查询，newValue的值为undefined
   ```

3. location和navigator实际上都是window对象的属性。

4. 每个框架都拥有自己的window对象，并且保存在frames集合中。top对象始终指向最外层框架，parent指向当前框架的父框架。self指向自己

5. 窗口坐标**：IE,Safafi,Opera和Chrome提供了**window.screenLeft**,**window.screenTop**属性，Firefox,Safari,Chrome提供了**window.screenX**,**window.screenY属性。分别用来表示窗口相对屏幕左边和上边的位置。不同的浏览器可能标准不一样，IE和Opera是屏幕左边和上边到window对象表示的页面可见区域的距离。而Chrome,Firefox,Safari中则是整个浏览器窗口相对屏幕的坐标值。

6. window.moveTo()接收的是新位置的x和y坐标值，而window.moveBy()接收的是水平和垂直方向上移动的像素数（右移下移为正）。一般被禁用。

7. **窗口大小**：IE9+,Firefox,Safari,Opera和Chrome都提供了**innerWidth,innerHeight,outerWidth, outerHeight**。在大部分浏览器中inner表示页面大小，outer表示浏览器大小。基本所有浏览器中都在document.documentElement.clientWidth和document.documentElement.clientHeight中保存了页面视口的信息。

8. 使用resizeTo()和resizeBy()可以调整浏览器窗口的大小。一般被禁用。

9. 使用**window.open()**方法既可以导航到一个特定的URL，也可以打开一个新的浏览器窗口。这个方法接收4个参数：**要加载的URL、窗口目标、一个特性字符串**和一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值。第二个参数等同于a标签中的target，可以设置为:_self, _parent, _top, _blank这些特殊值，也可以设置为目标URL的窗口或框架名。

   如果传递的第二个参数并不是一个已经存在的窗口或框架，那么此方法会根据第三个参数传入的字符串弹出一个新窗口或新标签页。如果没有传入第三个参数，那么就会打开一个带有全部默认设置的新浏览器窗口。在不打开新窗口的情况下，会忽略第三个参数。此特性字符串主要有如下属性：height,left,top,width，表示新窗口的大小和坐标，其他属性大部分被禁用。

10. window.open()返回新窗口的window对象，可以使用其close()方法关闭窗口。新窗口的window对象中还有一个opener()方法，顾名思义。

11. 超时调用**setTimeOut()**方法接收两个参数：要执行的代码和以毫秒表示的时间。此时间为JavaScript再经过多长时间把当前任务添加到任务队列中去。当任务队列为空时，被添加的任务才会执行。可以使用**clearTimeOut()**方法取消未执行的超时调用计划。该方法接收setTimeOut()方法返回的**超时调用ID**。

12. 间歇调用**setInterval()**方法用法与setTimeOut()方法类似，看实例：

    ```javascript
    var num=0;
    var max=10;
    var intervalId=null;
    function incrementNumber(){
        num++;
        console.log(num);
        if(num==max){
            clearInterval(intervalId);
            alert("Done");
        }
    }
    intervalId = setInterval(incrementNumber,1000);
    ```

    每秒输出一次，输出十次后取消此间歇调用。但是间歇调用可能会后一个间歇调用在前一个间歇调用结束之前启动。一般使用超时调用去模拟间歇调用来解决这个问题：

    ```javascript
    var num=0;
    var max=10;
    function incrementNumber(){
        num++;
        if(num<max){
            console.log(num);
            setTimeOut(incrementNumber,1000);
        }else{
            alert("Done");
        }
    }
    setTimeOut(incrementNumber,1000);
    ```

13. 三种系统对话框alert(),confirm(),prompt()与在浏览器中显示的网页没有关系，也不包含html和css。

    - alert("xxx")：无返回值，用户只能点确定
    - confirm("xxx")：用户点确定时返回true,点取消时返回false
    - prompt("some message",默认值)：点确认时返回输入的值，点取消时返回null。

    如果一次弹出多于1个对话框，大部分浏览器会有显示一个复选框，帮助用户阻止后续弹框。



### location对象

1. window.location和document.location引用的是同一个对象。location对象不仅保存着当前文档的信息，还将URL解析为独立的片段。下表列出了location对象的绝大部分属性：

   | 属性名   | 例子                                 | 说明                           |
   | -------- | ------------------------------------ | ------------------------------ |
   | hash     | "#bottom"                            | 返回URL中的hash（带#号）       |
   | host     | "www.wrox.com:80"                    | 返回服务器名称和端口号(如果有) |
   | hostname | "www.baidu.com"                      | 返回不带端口号的服务器名       |
   | href     | "http://www.baidu.com/aa.html?ff=12" | 返回完整的URL                  |
   | origin   | "http://www.baidu.com"               |                                |
   | pathname | "/articles/44545.html"               | 返回目录和（或）文件名         |
   | port     | "8080"                               | 返回端口号                     |
   | protocol | "http:"                              | 返回使用的协议                 |
   | search   | "?ff=12"                             | 返回URL的查询字符串(?开头)     |

   href = protocol+hostname+port+pathname+search+hash

   关于hash的详情，参考https://www.cnblogs.com/joyho/articles/4430148.html

2. 可以创建一个函数来解析查询字符串：

   ```javascript
   function getQueryStringArgs(){
       var qs = location.search.length>0? location.search.substring(1):"";
       var items = qs.length>0? qs.split("&"):[];
       var args = {};
       var name=null,value=null,item=null;
       for(var i=0;i<items.length;i++){
           item = items[i].split("=");
           name = decodeURIComponent(item[0]);
           value = decodeURIComponent(item[1]);
           if(name.length){
               args[name] = value;
           }
       }
       return args;
   }
   ```

3. location对象的位置操作：向location.assign()方法中传递一个URL,修改location,location.href,hash,search,hostname,pathname等等都会改变页面的位置。每次修改location的属性(**除了hash**)外，页面都会以新URL重新加载。

4. **通过上述任意一种方式修改URL后，浏览器的历史记录就会生成一条新记录**，从而用户可以点**击后退按钮**来回到上个页面。使用**location.replace()方法，则会禁用这种行为**。

5. **location.reload()**方法在不传参数时会以最有效的方法重新加载页面。如果要强制从服务器重新加载，可以传递参数true。reload()方法之后的代码不一定会调用，最好把reload()放在最后一行。



### navigator对象

1. navigator对象的属性：

   ![1538138601380](C:\Users\hama\AppData\Local\Temp\1538138601380.png)

2. navigator对象中的plugins对象是个数组。包含了浏览器安装的插件的信息。数组项的name属性为插件的名字。利用它可以做插件检测。

   ```javascript
   //IE中无效
   function hasPlugin(name){
       name = name.toLowerCase();
       for(var i=0;i<navigator.plugins.length;i++){
           if(navigator.plugins[i].name.toLowerCase().index(name)>-1){
               return true;
           }
       }
       return false;
   }
   alert(hasPlugin("Flash"));
   alert(hasPlugin("QuickTime"));
   ```

   在IE中检查特定插件比较麻烦，要检查特定插件，必须知道其COM标识符。

   ```javascript
   //检测IE中的插件
   function hasIEPlugin(name){
       try{
           new ActiveXObject(name);
           return true;
       }
       catch(ex){
           return false;
       }
   }
   hasIEPlugin("ShockwaveFlash.ShockwaveFlash");  //检测Flash
   hasIEPlugin("QuickTime.QuickTime");  //检测QuickTime
   ```

   合二为一：

   ```javascript
   function hasFlash(){
       return hasPlugin("Flash")? 		 true:hasIEPlugin("ShockwaveFlash.ShockwaveFlash");
   }
   function hasQuickTime(){
       return hasPlugin("QuickTime")? true:hasIEPlugin("QuickTime.QuickTime");
   }
   ```



### screen对象

	作用不大，包含浏览器窗口外部的显示器的信息。availHeight为可用屏幕像素高度。



### history对象

history对象保存着用户的上网的历史记录，从窗口被打开的那一刻算起。

1. **history.go(),history.forward(),history.back()**方法可以在用户的**历史记录中跳转**

   ```javascript
   history.go(1);  //前进一页
   history.go(-2);  //后退两页
   history.go("baidu.com");  //跳转到最近包含baidu.com的页面
   history.back();  //后退一页
   history.forward();  //前进一页。
   ```

2. history对象还有一个length属性，保存着当前窗口拥有的历史记录的数量。包含自身，谷歌和火狐浏览器在窗口的第一个页面,history.length为1。















