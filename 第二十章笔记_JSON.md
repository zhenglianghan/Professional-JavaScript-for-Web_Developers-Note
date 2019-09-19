# 第二十章 JSON

1. JSON(JavaScript Object Notation，JavaScript对象表示法)中的字符串必须使用双引号。

2. JSON中对象的属性名必须加双引号。

3. JSON.stringify()输出的JSON字符串默认不包括任何空格字符或缩进。

4. 在序列化JavaScript对象时，所有函数及原型成员都会被有意忽略，不体现在结果中。此外，值为undefined的任何属性也会被跳过。

5. JSON.stringify()除了要序列化的JavaScript对象外，还可以接收另外两个参数。第一个参数时过滤器，可以是一个数组，也可以是一个函数；第二个参数时一个选项，表示是否在JSON字符串中保留缩进。

6. 第二个参数如果是数组，则结果中将只包含数组中列出的属性。

   ```javascript
   var book = {
       "title" : "Professional JavaScript",
       "authors" : [
           "Nicholas C. Zakas"
       ],
       edition : 3,
       year : 2011
   }
   var jsonText = JSON.stringify(book,["title","edition"]);  //{"title":"Professional JavaScript","edition":3}
   ```

   第二个参数如果是函数，则作为过滤函数。如果返回值是undefined，则相应属性会被忽略。

   ```javascript
   var jsonText = JSON.stringify(book,function(key,value){
       switch(key){
           case "authors":
               return value.join(",");
           case "year":
               return 5000;
           case "eidtion":
               return undefined;
           default:
               return value;
       }
   });  //{"title":"Professional JavaScript","authors":"Nichoals C. Zakas","year":5000}
   ```

7. 第三个参数如果是一个数字，则为缩进的空格数，如果是字符串，则为每行前面的字符串。数字或者字符串长度超过10都会自动转换为10。使用了第三个参数后结果字符串中都插入了换行符。

8. 可以给对象添加一个toJSON()方法，用于返回其自身的JSON数据格式。

9. 假设把一个对象传入JSON.stringify()，序列化该对象的顺序如下：

   (1) : 如果存在toJSON()方法而且能通过它取得有效的值，则调用该方法。否则，返回对象本身。

   (2) : 如果提供了第二个参数，应用这个函数过滤器。传入函数过滤器的值是第(1)步返回的值。

   (3) : 对第(2)步返回的每个值进行相应的序列化。

   (4) : 如果传入了第三个参数，执行相应的格式化。

10. 解析方法parse()也可以传入另一个参数，该参数是一个函数，将在每个键值对上调用，返回一个值。功能与stringify()方法的过滤函数类似。



