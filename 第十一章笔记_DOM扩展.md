# 第十一章 DOM扩展

### 选择符API

1. SelectorsAPI Level 1 的核心是两个方法：querySelector()和querySelectorAll()。在兼容的浏览器中，可以通过Document及Element类型的实例调用它们。

2. **querySelector()**方法，接收一个CSS选择符，返回与该模式匹配的第一个元素，如果没找到则返回null，使用示例：

   ```javascript
   var body = document.querySelector("body");
   var myDiv = document.querySelector("#myDiv");
   var selected = document.querySelector(".selected");
   ```

   通过Document类型调用querySelector（）方法时，会在文档范围类搜索，而通过Element类型调用此方法时，会以此元素为起点搜索。可以传入很复杂的CSS选择符。如果传入不被支持的选择符会抛出错误。

3. **querySelectorAll()**方法，同样接收一个CSS选择符，返回一个**静态的**NodeList实例。

4. matchesSelector()方法，接收一个参数，即CSS选择符，如果调用元素与该选择符匹配，返回true；否则返回false。目前来说没有浏览器实现这个方法，IE9+通过msMatchesSelector()方法，Firefox3.6+通过mozMatchesSelector()方法，Safari5+和Chrome通过webkitMatchesSelector()方法来支持该方法。另外火狐和谷歌浏览器有matches方法来支持该方法。

### 元素遍历

1. 对于元素间的空格，浏览器通常会返回文本节点。这样操作很麻烦。Element Traversal规范新定义了一组属性：

   - childElementCount : 返回子元素（不包括文本节点和注释）的个数
   - firstElementChild:指向第一个子元素；firstChild的元素版
   - lastElementChild:指向第一个子元素；lastChild的元素版
   - previousElementSibling:指向上一个同胞元素;previousSibling的元素版
   - nextElementSibling:指向上一个同胞元素;nextSibling的元素版

   另外，还有个不知道拿个API里面来的属性**children**,指向一个包含所有子元素的**动态的**HTMLCollection对象。

### HTML5

1. **getElementsByClassName()**方法接收一个参数，即一个包含一或多个类名的字符串（**多个类名用空格隔开，顺序无所谓**），返回一个NodeList。可以在文档节点上调用也可以在元素节点上调用。
2. HTML5为所有元素添加了一个很好用的**classList**属性，这个属性是新集合类型DOMTokenList的实例。访问这个类型可以用方括号表示法或item()方法，另外，这个新类型还定义如下方法：
   - **add(value)** : 将给定字符串添加到列表中。如果值已经存在则不再添加。无返回值
   - **contains(value)** : 表示列表中是否存在给定的值。返回布尔值
   - **remove(value)** : 从列表中删除给定字符串。无返回值
   - **toggle(value)** : 列表中没有value则添加并返回true，有则删除并返回false。
3. HTLM5添加了辅助管理DOM焦点的功能。首先是**document.activeElement**属性，指向获得焦点的元素的引用。在文档加载期间，document.activeElement属性值为null，在刚刚加载完成时指向document.body。另外新增了**document.hasFocus()**方法，用于确定文档是否获得了焦点。
4. HTML5扩展了HTMLDocument，增加了新的功能，为document对象增加了如下属性：
   - **document.readyState** : 文档正在加载时值为**loading**,文档加载完成时值为**complete**
   - **document.compatMode** : 标准模式下值为**CSS1Compat**,混杂模式下值为**BackCompat**
   - **document.head** : 指向head标签

5. `Document.characterSet` 只读属性返回当前文档的字符编码。该字符编码是用于渲染此文档的字符集，可能与该页面指定的编码不同。（用户可以重写编码方式。）

   `document.charset` `和document.inputEncoding` 属性是`document.characterSet` 的遗留别名。不要再使用它们。

6. 自定义数据属性。HTML5规定可以为元素添加非标准的属性，但是要添加前缀data-，目的是为元素提供与渲染无关的信息，或者提供语义信息。添加了自定义属性后，可以通过元素的**dataset**属性来访问自定义属性的值。访问键名时需去掉data-前缀。在JS设置新自定义属性时驼峰命名法会转换成-的形式写到标签中。使用示例：

   ```javascript
   <div id="myDiv" data-myname="nico"></div>
   var div = document.getElementById("myDiv");
   alert(div.dataset.myname);  //nico
   div.dataset.myname = "nicoco"  //设置新值
   ```

   使用DOM1只能使用getAttribute()方法来访问自定义属性，比较麻烦。

7. **innerHTML**属性返回与调用元素的所有子节点对应的HTML标记（不同浏览器返回值可能不同）。设置它的值会被解析为DOM子树，替换调用元素原来的所有子节点。并不是所有元素都支持innerHTML属性。

8. **outerHTML**属性返回与**调用元素及其所有子节点**对应的HTML标记（不同浏览器返回值可能不同）。设置它的值会被解析为DOM子树，并完全替换调用元素。并不是所有元素都支持outerHTML属性。

9. 使用这些属性删除元素时，元素与事件处理程序（或JavaScript对象）之间的绑定关系在内存中并没有一并删除。这可能会导致浏览器的**内存占用问题**。

10. 使用元素的**scrollIntoView()**方法，会让浏览器的视口滚动到此元素。

### 专有扩展

1. **children**属性,指向一个包含所有子**元素**的**动态的**HTMLCollection对象。

2. **contains()**方法。祖先节点调用此方法，接收一个参数，即要检测的后代节点。返回一个布尔值。

3. 插入文本**innerText**属性，在读取值时，它会按照由浅入深的顺序，将子文档树中的所有文本拼接起来。在写入值时，结果会删除元素的所有子节点，插入包含相应文本值的文本节点；另外，**设置值时对文本中存在的HTML语法字符（小于号，大于号，引号及和号）进行了编码。**

   **outerText**将作用范围扩大到了包含它的节点之外。








