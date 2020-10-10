&emsp;&emsp;昨天学了一个下午的webpack，自己呢是跟着视频学的，一开始还有点晕，后面跟着做笔记，然后自己按照笔记一点一点做出来，搭建完成之后再看看自己记的每个点的作用以及概念，也就理解了。这篇文章也是按照一个简单的搭建过程来写，看完之后，不懂的可以跟着流程一起搭建慢慢结合概念的点理解。

1.先新建一个文件夹，我这就取名为webpack2。然后在vs的终端输入


```bash
npm init -y
```

输入指令按下回车后，即会初始化生成的默认的package.json

2.安装webpack环境基础文件、在控制台输入

```bash
npm install webpack webpack-cli -D
```
输入回车安装完成后，此时webpack2文件夹目录下一共有两个文件，package.json和package-lock.json

3.这里我们以引入jquery为例，所以还得要安装jquery，输入

```bash
npm install jquery -S
```
4.然后在该文件夹下新建一个主页index.html以及js文件index.js
index.html编辑如下图![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612155451186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
index.js编辑如下图，是将单双行颜色分别显示为红色跟绿色
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612155555421.png)
此时在html中加入一句

```javascript
<script src="./index.js"></script>
```
然后保存，此时文件目录是如下图的
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020061215573073.png)
如此我们运行页面会有效果吗？
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612155837134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612155843531.png)
直接报错，因为浏览器无法解析import语法

5.所以此时需要用到webpack里面的包来解析该index.js里面的语法，在终端输入

```bash
webpack index.js -o dist/bundle.js
```
输入完成后会发现直接报错
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612160701554.png)
为什么呢？因为你的webpack不是全局安装的，仅仅是安装在当前这个文件目录下，所以我们要换一一种办法执行该命令

6.打开文件的package.json找到"script"
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612160929999.png)
然后在后面加上一个自定义指令

```javascript
"start":"webpack index.js -o dist/bundle.js"
```
加上后如图，这条指令的的意思呢就是用webpack 将index.js处理后-o就是output输出的意思，输出到新建的dist/目录下铭文bundle.js的文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612161128822.png)
7.保存后，在终端输入

```bash
npm run start
```
那么就可以看到新生成一个dist目录下的bundle.js文件，这就是已经处理过的浏览器可以处理的js文件了，所以我们还要到index.html里面将script标签里面引入的js文件修改一下，修改为

```javascript
<script src="./dist/bundle.js"></script>
```
此时我们保存，再运行一下该文件，可以看到已经有效果了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612161735428.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
8.此时到这里，简易的一个webpack可以说已经搭建好了，但是此时如果我们修改index.js里面的颜色，刷新页面，会发现没有效果，为什么呢，因为你没有将index.js重新处理成浏览器可以处理的js文件，所以我们修改了index.js还得重新运行
`npm run start`
然后再刷新页面才会有效果，这样太麻烦了，开发效率很低，所以我们接下来要引入安装【热重载】，热重载的功能就是你即时修改index.js文件，后台可以即时处理你的index.js文件，那么浏览器也就能实时显示效果了，能够提高开发效率
首先在终端输入

```bash
npm install webpack-dev-server -D
```
安装完成之后要修改package.json里面script自定义指令为

```javascript
"start": "webpack-dev-server index.js -o dist/bundle.js"
```
然后我们在index.js修改单行的颜色为黄色
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612164503501.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
然后运行`npm run start`会看到终端中有一个端口提示
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612164252489.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
打开该端口页面，然后我们会发现怎么还是红绿颜色呢
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612164610585.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
我们刚才明明已经修改了奇数行的颜色为黄色，为什么不显示呢。这里我们就要注意了，因为热重载生成的bundle.js文件其实是缓存在该目录的内存里面的，因为运行内存肯定要比运行硬盘里面的文件更快，这样才能达到【热重载】即时更新的效果，所以我们要到index.html主页修改我们的script标签引入文件，修改为

```javascript
<script src="./bundle.js"></script>
```
即引入内存中的虚拟bundle.js文件，此时我们再打开我们的端口页，会发现已经有效果了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612165142222.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
9.补充增加一些指令以及指令里面的功能
①.终端操作`ctrl`+`C`，再输入y，可以停止当前已经启动端口页
②.修改package.json里面script自定义指令为

```javascript
"start": "webpack-dev-server index.js -o dist/bundle.js --open --port 8002"
```
即在原指令后加上--open 跟 --port 8002
解释一下，--open的作用是运行完 npm run start 该指令会自动直接用默认浏览器打开该端口页，而--port 8002 即将原8080端口号改成8002
③.在package.json里面script自定义指令后增加一个build指令

```javascript
"build": "webpack index.js -o dist/bundle.js"
```
改完之后如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612171341720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
这个build用来干啥的，之后会讲

10.其实我们前面的做法将自定义指令直接写死在package.json不是最好的做法，所以我们接下来要讲一种更好的方法，先在webpack2目录下新建一个webpack.config.js配置文件
然后在里面写入

```javascript
var path = require('path');
module.exports = {
	entry:path.join(__dirname,'index.js'),
	output:{
		path:path.join(__dirname,'dist'),
		filename:'bundle.js',
	 },
}
```
具体这个代码片段什么意思，就先不做过多解释，其实就是将index.js -o dist/bundle.js这一个片段换一种写法写在该配置文件。
写好之后，我们再将package.json里面的script下的自定义指令都修改一下
修改为

```javascript
"scripts": {
	"test": "echo \"Error: no test specified\" && exit 1",
	"start": "webpack-dev-server --config webpack.config.js --open --port 8002",
	"build": "webpack --config webpack.config.js"
  },
```
然后保存之后，在终端输入`npm run start`能看到跟刚才效果是一样的，只是这种写法有利于我们后续引入别的处理模块，比如处理css的css-loader，style-loader之类的

11.在webpack2新建一个index.css文件，然后写入设置body背景颜色的代码，如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612172722297.png)
然后在index.js中加入一行代码

```bash
import './index.css'
```
即把css文件当成一个模块去引入，然后我们输入`npm run start`跑起来，会发现直接报错，为什么呢，因为我们没有能处理的css文件的处理模块，所以我们就需要安装这些模块
在终端输入

```bash
npm install css-loader style-loader -D
```
安装这两个模块，安装好之后，我们还得把这两个模块引入来处理，所以要在webpack.config.js文件中写入代码来引入，增加

```javascript
module:{
        rules:[
            {test:/.css$/,use:["style-loader","css-loader"]},
        ]
    },
```
添加完之后如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612175712121.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
此时在终端输入`npm run start`，会发现页面css效果已经出来了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612174125818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
12.上面11点讲的是增加css文件，如果我增加的是less文件呢？在webpack2文件中新建一个a.less文件然后修改一下index.html以及a.less如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612174831644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612174836446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
此时在index.js里面加入一行
```bash
import './a.less'
```
然后输入`npm run start`，肯定直接报错，因为我们webpack中没有能处理less文件的处理模块，跟上面是一样的，所以我们要安装一下，终端输入
```bash
npm install less-loader less -D
```
然后还要修改webpack.config.js配置文件，修改加入
```javascript
{test:/.less$/,use:["style-loader","css-loader","less-loader"]},
```
修改后如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612175730554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
然后我们终端输入`npm run start`跑起来，能看到页面效果如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612180610819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
之后你还要处理什么文件，都是如此的流程，具体你处理的文件需要引入安装什么新的模块可以上网查一下

13.安装使用htmlwebpackplugin插件，这个软件可以自动将你写的js文件引入到你的html主页上，动态引入
先在终端输入以下安装
```bash
npm install html-webpack-plugin -D
```
然后将以下代码添加到webpack.config.js配置文件中
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
```
```javascript
plugins:[
        new HtmlWebpackPlugin({
            template:path.join(__dirname,'./index.html'),
            filename:'index.html',
        })
    ]
```
添加位置如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612182331603.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNTk3NTg5,size_16,color_FFFFFF,t_70)
然后我们就可以将index.html原来的script
```javascript
<script src="./bundle.js"></script>
```
直接注释掉，此时我们保存并用`run npm start`运行，然后能看到页面效果是正常显示的，按F12检查页面，能看到如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612182639760.png)
虽然我们注释了原来的script，但是该插件自动动态引入了我们需要的js文件

14.最后讲一下前面的build自定义指令，因为这些文件都是我们开发环境下使用的文件，我们项目最后上线是不需要那么多没用到的文件以及模块的，所以我们的build指令就是最后生成一个我们页面仅仅需要的文件，那么我们就可以只拿所需，减少浪费不必要的空间。
我们运行 `npm run build`，然后能看到生成了我们所需上线文件在dist目录下，如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200612183050742.png)
以上就是有关webpack简单的一些内容，如果哪里有写错的，或者写的不好的，还请大家在评论里面指出以便我修正。
