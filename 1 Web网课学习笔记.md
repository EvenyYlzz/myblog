## Web网课学习笔记

1.今天做自己的导航网页对body做背景的整个页面发现一个问题，但是设置完 background属性之后刷新页面发现背景图并没有填充整个页面，也就是没有自适应页面。查了之后加了一段代码。

原代码：

<body background="imagines/beijing.png" >
修改后代码多加个一个style样式：

<body background="imagines/beijing.png"     
    style=" background-repeat:no-repeat ;      
  background-size:100% 100%;       
  background-attachment: fixed ;" >
修改后就将整个页面填充好了，重点是这个background-attachment: fixed 它可以将图片固定在页面位置，不会随着你鼠标的滚动滑动页面时而消失。



2.突然想着给我自己做的导航主页做一个网页的背景音乐，要显示播放按钮的

（搬运）

方式一：这种方式会显示播放器。

<video controls="" autoplay="" name="media">
<source src="音乐" type="audio/mpeg">
</video>　
方式二：这个方式主要是支持的播放器多。一定程度上解决了浏览器兼容的问题。
<embed src="music/We Don't Talk Anymore.mp3" autostart="true" loop="true" hidden="true">
</embed>
方式三：这个不显示播放器。
<audio autoplay="" loop="">
<source src="http://mi.0w0.im/Letter_Song.mp3">
</audio>
方式四：

<bgsound src=背景音乐链接地址 loop=-1>
方式五：

<audio src="music/We Don't Talk Anymore.mp3" id="aud" autoplay="autoplay" controls="controls" preload="auto">
</audio>


3.我在自己界面添加了音频后，发现那个播放界面有点大，导致我的界面不美观，我想设置他的宽度，但是我用div定义区域大小但是不起作用后面找了好久才发现是用其自带的属性控制的

<video width="" height="" >
设置完之后就完美解决了



4.margin:0 auto居中的前提是我们为div设置了宽度，如果没有设置宽度，就算使用了改css属性，也不会取作用。因此，首先要检查一下是否设置宽度。

检查一下div是否使用了float:left或float:right,如果使用了float:left或float:right，margin:0 auto也不会取作用



5.while循环关闭窗口会导致首页刷新无响应为什么呢?（待解决）





6.absolute属性会令margin的0 auto属性的居中失效



7.transform进行移动不能分两行写，否则只进行后面的transform的操作

transform: ........

transform: ........  如这样则只会执行第二行的



8.boxing-sizing：border-box的用途

使用了box-siziing:border-box之后，div把border的宽度也计算进了width和height



9.canvas 

这个画布由于没有看书系统地学，使用起来的时候产生了很多奇怪的错误，用js做画板的时候，发现这个鼠标在哪，但并不在哪画，后来搜了一下文章，才发现这个canvas的高宽只能在标签内进行设置，不然会导致很多问题。参考https://www.cnblogs.com/jacobb/p/6814317.html



10.translate 跟 动画animation不能用在一个元素上

要多嵌套上一个div元素，否则translate会失效



11.HTML、XML、XHTML 有什么区别?

HTML即是超文本标记语言（Hyper Text Markup Language），是最早写网页的语言，但是由于时间早，规范不是很好，大小写混写且编码不规范,是语法较为松散的、不严格的Web语言

XHTML是升级版的html（Extensible Hyper Text Markup Language），对html进行了规范，编码更加严谨纯洁，也是一种过渡语言，html向xml过渡的语言。实际上XHTML 与 HTML 4.01 标准没有太多的不同。

XML是可扩展标记语言（Extensible Markup Language），是一种跨平台语言，编码更自由，可以自由创建标签（
比如像下面这样创建：
<note><to>Tove</to><from>Jani</from><heading>Reminder</heading><body>Don't forget me this weekend!</body><te>
），主要用于存储数据和结构，可扩展

HTML和XML的区别：
XML 被设计用来传输和存储数据，其焦点是数据的内容。
HTML 被设计用来显示数据，其焦点是数据的外观。
HTML 旨在显示信息，而 XML 旨在传输信息。
XML在定义标记时区分大小写，而HTML标记不区分大小写。


