# 第十五章 使用canvas绘图

1. 要使用canvas元素，必须要先设置其width和height属性来作为画布的大小,/canvas不可省略

   ```html
   <canvas id="drawing" width="600px",height="600px">Draw here</canvas> 
   ```

2. 获取上下文使用getcontext()方法,获取2D上下文传入"2d",获取WebGL上下文传入"webgl"或者"experimental-webgl"

   ```javascript
   var drawing = document.getElementById("drawing");
   if(typeof drawing.getContext=="function"){
       var context = drawing.getContext("2d");  //获取2D上下文
   }
   ```

3. 2D上下文的两种基本绘图操作时填充和描边，使用context.fillStyle和context.strokeStyle属性可以改变填充和描边的颜色。

4. 与绘制矩形相关的三种方法：fillRect(),strokeRect(),clearRect()，这三个方法都传入四个参数：矩形的x,y坐标，矩形的宽高。这些参数的单位都是像素。

5. 描边线条的宽度由lineWidth决定，lineCap属性可以控制线条末端的形状是平头，圆头，还是方头（"butt","round","square"）,lineJoin属性可以控制线条相交的方式是圆交，斜交，还是斜接（"round","bevel","miter"）。

6. 绘制路径必须首先调用beginPath()方法，表示要开始绘制新路径，之后调用下列方法实际绘制路径

   - arc(x,y,radius,startAngle,endAngle,conterclockwise)，参数分别为圆心，半径，开始的角度（用弧度表示）和结束的角度，最后个参数为是否逆时针绘图，布尔值。
   - moveTo(x,y)：将绘图游标移动到（x,y），不画线。
   - lineTo(x,y)：从游标开始绘制一条直线到(x,y)为止。

   创建了路径之后，使用fill()方法填充路径或者使用stroke()方法描边路径。

7. 绘制文本有两个方法：fillText()和strokeText()，这两个方法都接收四个参数：要绘制的文本字符串，x坐标,y坐标和可选的最大像素宽度。一般使用fillText()，这两个方法都以下列三个属性为基础：

   - font：表示文本样式，大小及字体，如font = "bold,10px,Arial";
   - textAlign：表示文本的对齐方式可能的值有"start","end","left","right","center"，当为start时，以fillText()传入的x坐标作为文本的起点，end时为终点，center时为中点
   - textBaseLine：文本的基线，设为"middle"时y坐标为文本的垂直中点。

8. canvas绘图示例：

   ```javascript
   window.onload = function(){
   		var drawing = document.getElementById("drawing");
   		if(drawing.getContext){
   			var context = drawing.getContext("2d");
   			context.beginPath();
   			context.arc(500,200,50,0,2*Math.PI,false);
   			context.moveTo(550,450);
   			context.lineTo(550,625);
   			context.moveTo(450,450);
   			context.lineTo(450,625);
   			context.strokeStyle = "#FFB6C1";
   			context.lineWidth = "10";
   			context.stroke();
   			context.strokeRect(410,250,180,200);
   			context.font = "bold 18px Arial";
   			context.textAlign = "center";
   			context.textBaseline = "middle";
   			context.fillText("蛤",500,200);
   		}
   	}
   ```

   效果：

   ![](C:\Users\hama\Pictures\1.png)

