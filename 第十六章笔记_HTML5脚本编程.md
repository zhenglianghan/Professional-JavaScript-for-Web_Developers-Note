# 第十六章 HTML5脚本编程

### XDM

1. 跨文档消息传递(XDM)可以使用window对象的**postMessage()**方法，接收两个参数：一条消息和一个表示消息接收方的域。接收到XDM消息时会触发window对象的**message事件**，事件对象包括三个属性：
   - data：作为postMessage()第一个参数传入的字符串数据
   - origin：发送消息的文档所在的域，例如"http://www.wrox.com"。
   - source：消息发送方文档的window对象的代理。这个代理对象主要用于在发送上一条消息的窗口中调用postMessage()方法。不能通过代理对象访问本身的window对象的其他任何信息。

### 原生拖放

1. 在拖动某元素时，将依次触发下列事件：

   - dragstart：拖动开始
   - drag：拖动中，会反复触发
   - dragend：拖动结束

   上述三个事件的目标都是被拖动的元素

2. 当某个元素被拖动到一个有效的放置目标上时，下列事件会依次发生：

   - dragenter：进入放置目标边界时触发
   - dragover：在放置目标内移动时，重复触发此事件
   - dragleave/drop：分别在被拖动元素离开放置目标时，被拖动元素被放置时触发

   这四个事件的目标都是放置元素。

3. 取消无效放置元素（如div）的dragover和drop事件的默认行为，可以使无效放置元素有效。

4. dataTransfer对象是事件对象的一个属性，用于从被拖动元素向放置目标传递字符串格式的数据。此对象有有两个方法：

   - setData()：接收两个参数：保存的数据类型和保存的数据
   - getData()：接收一个参数，保存的数据

   示例：

   ```javascript
   //Text
   event.dataTransfer.setData("text","hello");
   var text = event.dataTransfer.getData("text");
   //URL
   event.dataTransfer.setData("url","http://www.baidu.com");
   var text = event.dataTransfer.getData("url");
   ```

5. HTML5为所有HTML元素都规定了一个draggable属性，表示元素是否可用拖动，布尔值。

### 媒体元素

1. 使用audio标签和video标签可以嵌入媒体元素。用法如下：

   ```html
   <!DOCTYPE html>
   <html>
   <head>
   <script type="text/javascript">
   	window.onload = function(){
   		var btn = document.getElementById("btn");
   		var myVideo = document.getElementById("myVideo");
   		btn.onclick = function(e){
   			if(myVideo.paused){
   				myVideo.play();
   				btn.innerText ="暂停";
   			}
   			else{
   				myVideo.pause();
   				btn.innerText = "播放";
   			}
   		}
   	}
   </script>
   </head>
   <body>
   	<video id="myVideo" width="500" height="500">
   		<source src="resource/1.ogg" type="video/ogg"></source>
   		<source src="resource/1.mp4" type="video/mp4"></source>
   		你的浏览器不支持H5播放器
   	</video>
   	<button id="btn">播放</button>
   </body>
   </html>
   ```

2. 下面列出了大多数浏览器支持的视频方法、属性和事件：

   | 方法        | 属性        | 事件           |
   | ----------- | ----------- | -------------- |
   | play()      | currentSrc  | play           |
   | pause()     | currentTime | pause          |
   | load()      | videoWidth  | progress       |
   | canPlayType | videoHeight | error          |
   |             | duration    | timeupdate     |
   |             | ended       | ended          |
   |             | error       | abort          |
   |             | paused      | empty          |
   |             | muted       | emptied        |
   |             | seeking     | waiting        |
   |             | volume      | loadedmetadata |
   |             | height      |                |
   |             | width       |                |

   注释：在所有属性中，只有 videoWidth 和 videoHeight 属性是立即可用的。在视频的元数据已加载后，其他属性才可用。**注意，现在的浏览器，只有用户与文档交互后才能开始播放视频**

3. <audio.> 标签的属性：

   | 属性                                                         | 值       | 描述                                                         |
   | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
   | [autoplay](http://www.w3school.com.cn/tags/att_audio_autoplay.asp) | autoplay | 如果出现该属性，则音频在就绪后马上播放。                     |
   | [controls](http://www.w3school.com.cn/tags/att_audio_controls.asp) | controls | 如果出现该属性，则向用户显示控件，比如播放按钮。             |
   | [loop](http://www.w3school.com.cn/tags/att_audio_loop.asp)   | loop     | 如果出现该属性，则每当音频结束时重新开始播放。               |
   | [preload](http://www.w3school.com.cn/tags/att_audio_preload.asp) | preload  | 如果出现该属性，则音频在页面加载时进行加载，并预备播放。如果使用 "autoplay"，则忽略该属性。 |
   | [src](http://www.w3school.com.cn/tags/att_audio_src.asp)     | *url*    | 要播放的音频的 URL。                                         |











