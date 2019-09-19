# 第十二章 DOM2和DOM3

### DOM变化

1. document.implementation.createHTMLDocument()方法创建一个完整的HTML文档。该方法接收一个参数，即新创建文档的标题
2. isSameNode()和isEqualNode()，这两个方法都接收一个节点参数，并在传入节点与引用的节点相同或相等时返回true。相同指两个节点引用同一个对象。相等指的是两个节点是相同的类型，具有相等的属性。



### 样式

##### 访问元素的样式

1. 支持style特性的HTML元素在JS中都有一个对应的style属性。**此style对象是CSSStyleDeclaration的实例，包含着通过HTML的style特性指定的所有样式信息，但不包含与外部样式表或嵌入样式表经层叠而来的样式。对于短划线的css属性在JS中需用驼峰形式去访问**。其中float因为是保留字，对象上相应的属性名为cssFloat,IE上是styleFloat。

2. DOM2级样式规范为style对象定义了一些属性和方法。这些属性和方法在提供元素style特性值的同时，还可以修改样式。下面列出这些属性和方法：

   - cssText : CSS代码
   - length : 应用给元素的CSS属性数量
   - parentRule : 表示CSS信息的CSSRule对象
   - getPropertyPriority(propertyName) : 如果给定的属性使用了!important设置，则返回"important";否则返回空字符串。
   - **getPropertyValue(PropertyName)** : 返回给定属性的字符串值。**也可以用方括号里面传属性名**
   - **item(index)** : 返回给定位置的CSS属性的**名称**。**也可以用方括号里面传数字**
   - removeProperty(propertyName) : 从样式中删除给定属性
   - setProperty(propertyName,value,priority) : 将给定属性设置为相应的值，并加上优先权标志("important"或者一个空字符串)。

   使用示例：

   ```javascript
   //遍历元素的内联样式
   var prop,value,i,len;
   for(i=0,len=div.style.length;i<len;i++){
       prop = div.style[i];  //也可以用div.style.item(i)
       value = div.style[prop];  //也可以用div.style.getPropertyValue(prop)
       alert(prop + ":" + value);
   }
   ```

3. **计算的样式（所有样式表层叠而来并影响到当前元素的样式）**。DOM2级样式增强了document.defaultView，提供了**document.defaultView.getComputedStyle()**。这个方法接收两个参数：要取得计算样式的元素和一个伪元素字符串（例如：":after"）。如果不需要伪元素信息，第二个参数可以是null。这个方法**返回一个CSSStyleDeclaration对象**（与style属性的类型相同），其中包含当前元素的所有计算的样式。IE不支持这个方法，但是有个currentStyle属性。**记住所有计算样式都是只读的**。

##### 操作样式表

1. CSSStyleSheet类型表示的是样式表，包括外部样式表和嵌入样式表。CSSStyleSheet继承自StyleSheet，从StyleSheet接口继承而来的属性如下：

   - disabled : 表示样式表是否被禁用的布尔值
   - herf : 如果样式表是通过<link>包含的，则是样式的URL；否则是null
   - media : 当前样式表支持的所有媒体类型的集合
   - ownerNode : 指向拥有当前样式表的节点的指针
   - title : ownerNode中title属性的值
   - type : 表示样式表类型的字符串。对于CSS样式表而言这个字符串是"type/css"

   除了disabled之外，其他属性都是只读的。CSSStyleSheet类型增强了下列属性和方法：

   - **cssRules : 样式表中包含的样式规则的集合**。IE中为rules
   - **deleteRule(index)** : 删除cssRules集合中制定位置的规则。IE中为removeRule()
   - **insertRule(rule,index)** : 向cssRules集合中指定的位置插入rule字符串。IE中为addRule()

   应用于文档的所有样式表是通过**document.styleSheets**集合来表示的。可以用方括号表示法或item()方法访问每一个样式表。

2. CSSRule对象表示样式表中的一条规则。CSSStyleRule类型继承于CSSRule。CSSStyleRule对象包含下列属性：

   - **cssText** : 返回整条规则对应的文本。**只读**
   - parentStyleSheet : 当前规则所属的样式表。
   - **selectorText** : 返回当前规则的选择符文本。
   - **style**：一个CSSStyleDeclaration对象，可以通过它设置和取得规则中特定的样式值。
   - type：表示规则类型的常量值。对于样式规则，这个值是1。

   cssText属性与style.cssText属性类似，但并不相同。前者包含选择符文本和围绕样式信息的花括号，后者只包含样式信息。此外，CSSText是只读的，而style.cssText可以被重写。

   应用举例：

   ```css
   div.box{
   	background-color:blue;
   	width:100px;
   	height:200px;
   }
   ```

   ```javascript
   var sheet = document.styleSheets[0];
   var rules = sheet.cssRules||sheet.rules;
   var rule = rules[0];
   alert(rule.selectorText);  //div.box
   alert(ruel.cssText);  //包含选择符和花括号的css代码
   alert(rule.style.cssText);  //background......
   alert(rule.style.backgroundColor)  //"blue"
   ```

3. 创建规则使用**insertRule()**方法,IE中为addRule()方法：

   ```javascript
   sheet.insertRule("body { background-color:red}",0);  //插入成为样式表第一条规则
   sheet.addRule("body","background-color:red",0);  //IE
   ```

4. 删除规则使用**deleteRule()**方法，IE中为removeRule()方法，这两个方法都传入要删除规则的索引

##### 元素大小

1. 偏移量：

   - offsetHeigth : 元素在垂直方向上占用的空间大小，以像素计。包括元素的高度、（可以见的）水平滚动条的高度、上边框高度和下边框高度。
   - offsetWidth : 元素在水平方向上占用的空间大小，以像素计。包括元素的宽度、（可以见的）水平滚动条的宽度、上边框宽度和下边框宽度。
   - offsetLeft : 元素的左外边框至包含元素的左内边框之间的像素距离。
   - offsetTop : 元素的上外边框至包含元素的上内边框之间的像素距离。

   这些偏移量都是**只读的**。

   ![1538794073237](C:\Users\hama\AppData\Local\Temp\1538794073237.png)

2. 客户区大小：clientWidth和clientHeight，是指内容加内边距。不包括滚动条和边框。

   ![1538794103701](C:\Users\hama\AppData\Local\Temp\1538794103701.png)

3. 滚动大小：

   - scrollHeight : 在没有滚动条的情况下，元素内容（内容区加内边距）的总高度。
   - scrollWidth : 在没有滚动条的情况下，元素内容的总宽度。
   - scrollLeft : 被隐藏在内容区域左侧的像素数。通过设置这个属性可以改变元素的滚动位置
   - scrollTop : 被隐藏在内容区域上侧的像素数。通过设置这个属性可以改变元素的滚动位置

   ![1538794124261](C:\Users\hama\AppData\Local\Temp\1538794124261.png)

4. getBoundingClientRect()方法。这个方法会返回一个矩形对象，包括4个属性：left,top,right,和bottom（现在的浏览器还会有height和width）。这些属性给出了元素（**边框**）在页面中相对于**视口**的位置。right-left为offsetWidth(也就是这个矩形对象的width)。

5. 这四个确定大小的属性方法实例：

   ![1538796990671](C:\Users\hama\AppData\Local\Temp\1538796990671.png)

![1538797008772](C:\Users\hama\AppData\Local\Temp\1538797008772.png)

也可以用计算样式来得到元素的大小。



### 遍历

1. "DOM2级遍历和范围"模块定义了两个用于辅助完成顺序遍历DOM结构的类型：**NodeIterator**和**TreeWalker**。这两个类型能够基于给定的起点对DOM结构执行深度优先遍历操作。

2. **NodeIterator**类型比较简单，可以使用document.createrNodeIterator()方法创建它的实例。这个方法接收4个参数。

   - **root** : 想要作为搜索起点的树中的节点。
   - **whatToShow** : 表示要访问哪些节点的数字代码。
   - **fileter** : 是一个NodeFilter对象，或者一个表示应该接受还是拒绝某种特定节点的函数。
   - entityReferenceExpansion : 布尔值，表示是否扩展实体引用。HTML中没用。

   whatToShow参数时一个位掩码。这个参数的值以常量形式在NodeFilter类型中定义，如下所示：

   - NodeFilter.SHOW_ALL : 显示所有类型的节点。
   - NodeFilter.SHOW_ELEMENT : 显示元素节点。
   - NodeFilter.SHOW_ATTRIBUTE : 显示特性节点。
   - NodeFilter.SHOW_TEXT : 显示文本节点。
   - 其它九种节点类型都是类似的形式

   除了NodeFilter.SHOW_ALL之外，可以使用按位或操作符来组合多个选项。如下例：

   ```javascript
   var whatToShow = NodeFilter.SHOW_ELEMENT | NodeFilter.SHOW_TEXT
   ```

   对于第三个参数filter，可以传入一个NodeFilter对象（此对象只有一个方法：acceptNode()）,或者传入一个过滤函数，如果不需要过滤可以传入null :

   ```javascript
   var filter = {
       acceptNode : function(node){
           return node.tagName.toLowerCase() == "p" ?
               NodeFilter.FILTER_ACCEPT:NodeFilter.FILTER_SKIP;
       }
   }
   var filter = node=>node.tagName.toLowerCase() == "p" ?
               NodeFilter.FILTER_ACCEPT:NodeFilter.FILTER_SKIP;
   ```

   这样，就可以创建一个NodeIterator实例来深度遍历DOM树了。此类型两个主要方法是nextNode()和previousNode()，遍历到首尾时调用会返回null。刚创建的实例第一次调用nextNode()方法会返回设定的根节点。

   ```javascript
   var div = document.getElementById("myDiv");
   var iterator = document.createNodeIterator(div,NodeFilter.SHOW_ELEMENT,filter,false);
   var node = iterator.nextNode();  //myDiv
   while(node!=null){
       alert(node.tagName);
       node = iterator.nextNode();
   }
   ```

3. **TreeWalker**是NodeIterator的一个更高级的版本。除了nextNode()和previousNode()外，此类型还提供了下列用于在不同方向上遍历DOM结构的方法：

   - parentNode() : 遍历到当前节点的父节点。
   - firstChild()
   - lastChild()
   - nextSibling()
   - lastSibling()

   创建TreeWalker对象使用document.createTreeWalker()方法，此方法和document.createNodeIterator()方法接收的参数相同。对于第三个参数filter,过滤函数的返回值除了**NodeFilter.FILTER_ACCEPT和NodeFilter.FILTER_SKIP**之外，还可以返回**NodeFilter.FILTER_REJECT**，表示跳过相应节点及其整个子树。



### 范围

1. DOM2级在Document类型中定义了**createrRange()**方法来创建一个DOM范围。它有下列属性提供当前范围在文档中的位置信息：

   - **startContainer** : 包含范围起点的节点（即选区中第一个节点的父节点）。
   - **startOffset** : 范围在startContainer中起点的偏移量。如果startContainer 是文本注释那么就是跳过的字符数量。否则就是范围中第一个子节点的索引。
   - **endContainer** : 与上类似
   - **endOffert** : 与上类似
   - **commonAncestorContainer** : startContainer 和 endContainer 的共同祖先节点（最靠近它两的那个）

2. 使用范围最简单的方法就是使用**selectNode()**或**selectNodeContents()**，这两个方法都接受一个参数，即一个DOM节点，然后使用该节点中的信息来填充范围。其中selectNode()选择整个节点包括其子节点作为范围，而selectNodeContents()只选择子节点作为范围。

3. 要创建复杂的范围就得使用**setStart()**和**setEnd()**方法。这两个方法都接收两个参数：一个参照节点（作为startContainer 或endContainer ）和一个偏移量值（作为startOffset和endOffert）。

4. 创建了范围之后，就可以使用各种方法来对范围的内容进行操作了：

   - **range.deleteContents()**：删除范围。
   - **range.extractContents()** : 提取范围，此方法也会从文档中移除范围，但是会将移除的文档片段返回。
   - **range.cloneContents()** : 克隆范围，创建范围对象的一个副本。
   - **range.insertNode()** : 向范围选取的**开始处**插入一个节点。
   - **range.collapse()** : 折叠范围，传入true表示折叠刀范围的起点；传入false表示折叠刀范围的终点。

5. 比较DOM范围使用**compareBoundaryPoints()**方法。该方法接受两个参数：表示比较方式的常量值和要比较的范围。表示比较方式的常量值如下所示：

   - Range.START_TO_START(0)
   - Range.STAET_TO_END(1)
   - Range.END_TO_END(2)
   - Range.END_TO_START(3)

   返回值如下：

   - -1 : 第一个点在第二个点之前
   - 0 : 两点相等
   - 1 : 第一个点在第二个点之后

6. 清理范围使用**range.detach()**方法。

















