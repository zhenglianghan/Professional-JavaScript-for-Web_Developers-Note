# 第十章 DOM

### 节点层次

1. 文档节点是每个文档的根节点。

2. 每个节点都有一个nodeType属性，用于表示节点的类型。所有节点类型都继承自Node类型。一共有12种节点类型：

   - Node.ELEMENT_NODE(1)
   - Node.ATTRIBUTE_NODE(2)
   - Node.TEXT_NODE(3)
   - Node.CDATA_SECTION_NODE(4)
   - Node.ENTITY_REFERENCE_NODE(5)
   - Node.ENTITY_NODE(6)
   - Node.PROCESSING_INSTRUCTION_NODE(7)
   - Node.COMMENT_NODE(8)
   - Node.DOCUMENT_NODE(9)
   - Node.DOCUMENT_TYPE_NODE(10)
   - Node.DOCUMENT_FRAGMENT_NODE(11)
   - Node.NOTATION_NODE(12)

   因为有些浏览器没有这些常量表示，所以判断节点类型时最好用数字表示。

3. 节点关系：

   - **childNodes**属性：保存着直接子节点的NodeList对象，**动态的**。可以使用方括号表示法也可以使用item()方法获取保存在NodeList中的节点。
   - **parentNode**属性 : 该属性指向文档树中的父节点
   - previousSibling属性 : 指向前一个同胞节点，第一个节点的此属性为null
   - nextSibling属性 : 指向下一个同胞节点，最后一个节点的此属性为null
   - firstChild属性 : 顾名思义
   - lastChild属性 : 顾名思义
   - ownerDocument属性 : 所有节点都拥有此属性，指向节点所在文档的文档节点。

4. 操作节点：

   - appendChild()：用于向节点的childNodes列表的末尾添加一个节点，传入一个节点，返回新增的节点。
   - insertBefore() : 用于向节点的childNodes列表的指定位置添加一个节点，传入两个参数：要插入的节点和作为参照的节点。第二个参数为null时，插到最后。返回新增的节点。
   - replaceChild() : 接收两个参数：要插入的节点和要替换的节点。要替换的节点被此方法返回并从文档树中移除。
   - removeChild() : 从文档树中移除某节点，返回此节点。
   - cloneNode() : 克隆节点，接收一个布尔值参数，表示是否执行深复制。在为true时进行深复制，复制节点及其整个子节点树。在为false时进行浅复制，只复制节点本身。

##### Document类型

1. JS通过Document类型表示文档。在浏览器中document对象是HTMLDocument(继承自Document类型)的一个实例，表示整个HTML页面。document对象是window对象的一个属性。Document节点具有下列特征：
   - nodeType的值为9；
   - nodeName的值为"#document";
   - nodeValue的值为null;
   - parentNode的值为null;
   - ownerDocument的值为null;
   - 其子节点可能是一个DocumentType节点（最多一个）、Element节点（最多一个）、ProcessingInstruction或Comment。

2. Document节点的一些常用属性:
   - documentElement属性：始终指向HTML页面中的<html.>元素
   - body属性：直接指向<body.>元素
   - title属性：页面的标题，可修改
   - URL属性：页面完整的URL（即地址栏中显示的URL），只读
   - domain属性：页面的域名。与URL属性相关联，比如document.URL等于http://www.wrox.com/wang/,那么document.domain就等于www.wrox.com。可以通过修改这个属性来完成跨域：将两个页面的document.domain设置为一样的值，这样两个页面就可以互相访问对方包含的JavaScript对象了。但是domain属性只能被设置成自身或者其父域，例如www.wrox.com可以设置成wrox.com,而wrox.com不能设置为www.wrox.com。
   - referrer属性：来源页的URL，不可修改。

3. Document节点的查找元素的方法：
   - getElementById()：注意ID与页面元素的id严格匹配，包括大小写
   - getElementsByTagName()：返回一个类似NodeList对象的HTMLCollection对象，此对象也是一个**动态的**集合。可以使用方括号表示法和item()方法访问其中的项，*号表示全部。
   - getElementsByName()：与上面那个方法类似。

4. Document节点还有一些特殊集合。这些集合都是HTMLCollection对象，为访问文档提供了快捷方式：
   - document.anchors：包含文档中所有带name的a元素
   - document.forms：包含文档中的所有form元素
   - document.images：包含文档中所有的img元素
   - document.links：包含文档中所有带href的a元素

5. 可以使用document.implementation.hasFeature方法来检测浏览器实现的功能。此方法接收两个参数：要检测的DOM功能的名称及版本号。使用示例：

   ```javascript
   var hasXmlDom = document.implementation.hasFeature("XML","1.0");
   ```

   下表列出了可以检测的不同的值及版本号（常用）：

   ![1538576649674](C:\Users\hama\AppData\Local\Temp\1538576649674.png)

6. 文档写入方法write(),writeln().都接收一个字符串参数，即要写入到输入流中的文本。writeln()会在字符串末尾加一个\n。可以使用这两个方法向文档中动态加入内容，例如：

   ```html
   <script>
       document.write("<strong>"+(new Date()).toString()+"</strong>");
   </script>
   ```

   这个例子展示了在页面加载过程中输出当前日期和时间的的代码。

   open()和close()方法分别用来打开和关闭网页的输出流。

##### Element类型

1. Element类型用于表现XML或HTML元素，提供了对元素标签名、子节点及特性的访问。Element节点具有以下特征：
   - nodeType的值为1；
   - **nodeName的值为元素的标签名**；
   - nodeValue的值为null；
   - parentNode可能是Document或Element
   - 其子节点可能是Element、Text、Comment、ProcessingInstruction、CDATASection或EntityReference

2. tagName和nodeName属性返回的标签名在HTML中**大写**，在XML中与源代码一致，因此最好在比较前将标签名转换成相同的大小写形式。

3. 所有的HTML元素都由HTMLElement类型（此类型继承自Element类型）表示，不是直接通过这个类型，也是通过它的子类型来表示。每个HTML元素中都存在下列标准特性：

   - id : 元素在文档中的唯一标识符
   - title : 有关元素的附加说明信息
   - lang : 元素内容的语言代码，很少使用
   - dir : 语言的方向，很少使用
   - className : 与元素的class特性对应，即为元素指定的CSS类。不命名为class是因为class是保留字

   这些属性都可以用来取得或修改元素相应的特性值。

4. 取得特性**getAttribute()**方法, 注意此方法的参数与实际的特性名相同，因此要想得到class特性值，应该**传入class**而不是className，后者只有在通过对象属性访问特性时才用。通过这个方法也可以取得自定义特性。

   只有公有的（非自定义的）特性才会以属性的形式添加到DOM对象中。style特性和onclick这样的事件处理程序，其属性的值和通过getAttribute（）返回的值不相同。编程人员一般只使用对象的属性，在取得自定义特性时才使用getAttribute()。

5. 设置特性**setAttribute()**方法,传入两个参数：要设置的特性名和值。传入的特性名会统一转换为小写形式。当然也可以给属性赋值的方法设置特性，不过为DOM添加自定义属性，该属性不会自动成为元素的特性（getAttribute()方法返回null）。

6. 删除特性**removeAttribute()**方法，彻底删除特性。

7. Element类型是唯一使用**attributes**属性的DOM节点类型。这个属性包含一个**NamedNodeMap**，与NodeList相似，这也是个**动态**的集合。元素的每一个特性都由一个Attr节点表示，每个节点都保存在在NamedNodeMap对象中。可以通过方括号表示法和item()方法访问到集合中的项。这个属性可以拿来遍历元素的特性。

8. 创建元素**document.createElement()**方法。此方法接收一个参数,即要创建的元素的标签名。然后可以对其增强。这个方法也可以传入完整的元素标签：

   ```javascript
   var div = document.createElement("<div id=\"myDiv\" class=\"box\"></div>")
   ```

9. 说出来你可能不信以下代码在IE中<ul.>元素会有3个子节点，分别是3个<li.>元素。而其它浏览器<ul.>元素会有7个子节点，包括三个<li.>和4个文本节点。

   ```html
   <ul id = "myList">
       <li>Item 1</li>
       <li>Item 2</li>
       <li>Item 3</li>
   </ul>
   ```

   而将元素间的空白符全部删除，<ul>元素在任何浏览器中就只有3个子节点了。所以在执行某项操作之前，通常要检查一下nodeType属性。

10. 想获取特定标签名获得某个节点的子节点或后代节点可以通过元素的getElementByTagName()方法，这样搜索的起点会是当前元素。

##### Text类型

1. 文本节点由Text类型表示，包含的是可以照字面解释的纯文本内容。Text节点具有以下特征：

   - nodeType的值为3
   - nodeName的值为"#text"
   - nodeValue的值为节点包含的文本；
   - parentNode是一个Element；
   - 不支持子节点。

   可以通过**nodeValue**属性或者**data**属性来访问Text节点中包含的文本。

2. 可以使用**document.createTextNode()**方法创建文本节点。当然，新建的文本节点是个孤儿。

3. 使用元素节点的normalize()方法可以合并子节点的文本节点。

##### Comment类型

1. 注释在DOM中是通过Comment类型来表示的Comment节点具有下列特征：

   - nodeType的值是8；
   - nodeName的值是“#comment”
   - nodeValue的值是注释内容；
   - parentNode可能是Document或Element；
   - 不支持子节点。

   与text节点类似，此节点也可以用data与nodeValue属性获取内容。

##### DocumentType类型

1. DocumentType节点包含着与文档的doctype有关的所有信息，它具有下列特征：

   - nodeType的值为10；
   - **nodeName的值为doctype的名称；**
   - **nodeValue的值为null；**
   - parentNode是Document；
   - 不支持子节点。

   DocumentType对象被保存在document.doctype中，name属性保存着文档类型的名称。

**DocumentFragment类型**

1. 所有节点类型中，只有DocumentFragment在文档中没有对应的标记。DOM规定文档片段是一种轻量级的文档，可以包含和控制节点，但不会像完整的文档那样占用额外的资源。DocumentFragment节点具有下列特征：

   - nodeType的值为11；
   - nodeName的值为"#document-fragment"；
   - nodeValue的值为null；
   - parentNode的值为null；
   - 子节点可以是Element,ProcessingInstruction,Comment,Text,CDATASection或EntityReference。

   虽然不能把文档片段直接添加到文档中，但是可以把它作为一个仓库来使用。

   例如我们想为一个<ul>元素添加3个列表项，如果逐个地添加列表项将会导致浏览器反复渲染新信息。为了避免这个问题我们可以使用文档片段：

   ```javascript
   var fragment = document.createDocumentFragment();
   var ul = document.getElementById("myList");
   var li = null;
   for(var i=0;i<3;i++){
       li = document.createElement("li");
       li.appendChild(document.createTextNode(i+1));
       fragment.appendChild(li);
   }
   ul.appendChild(fragment);
   ```

##### Attr类型

1. 元素的特性在DOM中以Attr类型来表示。从技术角度来讲，特性就是存在于元素的attributes属性中的节点。特性节点具有下列特性：

   - nodeType的值为2；
   - nodeName的值为特性的名称；
   - nodeValue的值为特性的值；
   - parentNode的值为null；
   - 在HTML中不支持子节点。

   Attr对象有3个属性，name是特性名称，value是特性值，specified是一个布尔值，用以区分特性是在代码中指定的，还是默认的。



### DOM操作技术

1. 动态脚本：

   ```javascript
   //外部动态脚本
   function loadScript(url){
       var script = document.createElement("script");
       script.src = url;
       script.type = "text/javascript";
       document.body.appendChild(script);
   }
   loadScript("client.js");
   //内部动态脚本
   var script = document.createElement("script");
   script.text = "function sayHi(){alert('hi');}";
   script.type = "text/javascript";
   document.body.appendChild(script);
   ```

2. 动态样式：

   ```javascript
   //外部样式
   function loadStyles(url){
       var link = document.createElement("link");
       link.href = url;
       link.rel = "stylesheet";
       var head = document.getElementByTagName("head")[0];
       head.appendChild(link);
   }
   loadStyle("style.css");
   //内部样式
   function loadStyleString(css){
       var style = document.createElement("style");
       try{
           style.appendChild(document.createTextNode(css));
       }
       catch(e){
           style.styleSheet.cssText = css;
       }
       var head = document.getElementByTagName("head")[0];
       head.appendChild(style);
   }
   loadStyleString("body{background-color:red}");
   ```

3. 操作表格。为了方便构建表格，HTMLDOM为table,tbody,tr元素添加了一些属性和方法,主要是rows和cells，参见书籍282页。

4. **NodeList是动态的，所以遍历它时应该先将其长度存起来作为循环中止的条件，否则可能无限循环。**















