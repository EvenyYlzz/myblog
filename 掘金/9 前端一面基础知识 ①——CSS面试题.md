## 前言
&nbsp;&nbsp;&nbsp;&nbsp;根据慕课网的《快速搞定前端技术一面 匹配大厂面试要求》课程所整理的题目，陆续更新
## 常考面试题
### 1.如何理解语义化
- ①让人更容易读懂（增加代码可读性）
- ②让搜索引擎更容易读懂（SEO问题，让搜索排名靠前“钱”）
- ③正确的标签做正确的事情
- ④页面内容结构化

### 2.哪些标签是块级元素？哪些标签是内联（行内）元素？
- 块级（display：block/table）：`div`、`p`、`h1-h6`、`ul`、`ol`、`dl`、`li`、`header`、`footer`、`aside`、`section`、`article`、`form`、`table`等
- 内联（display：inline/inline-block）：`span`、`img`、`button`、`input`、`b`、`q`、`i`、`a`、`em`、`label`等
### 3.盒子模型的宽度（offsetWidth）如何计算？
```
#div1{
	width: 100px
    padding: 10px
    border: 1px solid #ccc
    margin: 10px
  }
```
&nbsp;&nbsp;&nbsp;&nbsp;首先要知道offsetWidth的概念是什么？

&nbsp;&nbsp;&nbsp;&nbsp;其实 offsetWidth = （ 内容宽度 + 内边距 + 边框 ）如下图
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9wMS1qdWVqaW4uYnl0ZWltZy5jb20vdG9zLWNuLWktazN1MWZicGZjcC9lZDU4NTA2MGYzNjY0NDBiYjlkZTAzMDUzZDg2ZjNmOH50cGx2LWszdTFmYnBmY3Atem9vbS0xLmltYWdl?x-oss-process=image/format,png)
&nbsp;&nbsp;&nbsp;&nbsp;所以这道题的答案也就是100px + 20px + 2px = 122px

&nbsp;&nbsp;&nbsp;&nbsp;那如果要让这里的offsetWidth = 100px要怎么做呢？

&nbsp;&nbsp;&nbsp;&nbsp;我们可以设置box-sizing ： border-box，这样设置之后，offsetWidth就会变成100px了，这里之后的内容宽度就会被减小，而内边距和边框的宽度是不变的，也就是此时的offsetWidth = 78px + 20px + 2px
### 4.CSS3新增了哪些属性？
&nbsp;&nbsp;&nbsp;&nbsp;只罗列部分常见、常用的属性
- 动画属性
  - @keyframes
  - animation
- 边框属性
  - border-image
  - border-radius
  - box-shadow
- 颜色属性
  - opacity
- **弹性盒子模型（Flexible Box）**
  - flex
  - align-content
  - align-items
  - align-self
  - justify-content
  - order
- 网格（Grid） 属性
  - grid-columns
  - grid-rows
- 超链接(Hyperlink) 属性
  - target
- 2D/3D 转换属性
  - transform
  - translate，可以控制元素的水平或垂直移动
  - scale，可以控制元素的放大缩小，以1为界限界定放大还是缩小
  - rotate，可以控制元素的旋转，按角度旋转
  - 设置translate3d，可以控制Z轴
  - skew，对元素进行倾斜
- 过渡（Transition） 属性
  - transition
  
### 5.margin上下左右分别设置负值，有何效果？
- top、left：设置负值，该元素会向上向左移动
- right：设置负值，右侧元素左移，自身不动
- bottom：设置负值，下方元素上移，自身不受影响
### 6.BFC理解和应用
#### 是什么？
- Block format content，块级格式化上下文
- 是一块独立渲染区域，内部元素的渲染不会影响边界以外的元素

#### 应用
- 清除浮动
- 作用：解决父元素高度塌陷问题；解决外边距重叠问题；清除内部元素浮动

#### BFC如何产生？
- 1. 根元素
- 2. float属性【不为】none;
- 3. display属性【为】:line-block,table-cell,line-flex,flex
- 4. position属性【不为】:static,relative;
- 5. overflow属性【不为】:visible;

### 7.float布局问题，手写clearfix
### 圣杯布局、双飞翼布局
- 需求：三栏布局，左中右，中间一栏最先加载和渲染，两侧内容固定，中间内容随着宽度自适应，一般用于pc网页
- 大体关键步骤：
  - ①使用float布局
  - ②两侧使用margin负值，以便和中间内容横向重叠
  - ③防止中间内容被两侧覆盖，一个用padding、一个用margin

详情建议参考文章 [css经典布局系列三——三列布局(圣杯布局、双飞翼布局)](https://juejin.im/post/6844903827603193863)
### 手写clearfix
```javascript
clearfix:after{
	content: ' '
    display: table
    clear: both
}
```

### 8.flex画色子
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9wOS1qdWVqaW4uYnl0ZWltZy5jb20vdG9zLWNuLWktazN1MWZicGZjcC82MTYxZjVlOWFkZGQ0NzM4YTFhMTFkOWU1NDZjYTdlOX50cGx2LWszdTFmYnBmY3Atem9vbS0xLmltYWdl?x-oss-process=image/format,png)
&nbsp;&nbsp;&nbsp;&nbsp;主要考察对flex的应用，直接上代码了
```javascript
<head>
    <style>
        .box {
            width: 200px;
            height: 200px;
            border: 2px solid #ccc;
            border-radius: 10px;
            padding: 20px;

            display: flex;
            justify-content: space-between;
            /* 两端对齐 */
        }

        .item {
            display: block;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background-color: #666;
        }

        .item:nth-child(2) {
            align-self: center;
            /* 上下左右居中对齐 */
        }

        .item:nth-child(3) {
            align-self: flex-end;
            /* 尾对齐 */
        }
    </style>
</head>

<body>
    <div class="box">
        <span class="item"></span>
        <span class="item"></span>
        <span class="item"></span>
    </div>
</body>

```
### 9.absolute和relative分别依据什么定位？
- absolute：相对于父级定位设置为relative的元素偏移，会一层层往上寻找，直至找到根元素，相当于浏览器页面定位。它必须搭配 top、bottom、left、right 这四个属性一起使用，用来指定偏移的方向和距离
- relative：相对于默认位置（即 static 时的位置）进行偏移，即定位基点是元素的默认位置。 它必须搭配 top、bottom、left、right 这四个属性一起使用，用来指定偏移的方向和距离
### 10.居中对齐有哪些实现方式？
水平居中：
- inline元素：text-align：center
- block元素：margin：0 auto
- 直接absolute：（ left：50% + margin-left：负值）
- translateX
- flex：（ display: flex;     justify-content: center;）

垂直居中：
- inline元素：height = line-height
- absolute：（ top：50% + margin-top：负值 ）
- translateY
- flex：（display: flex;     flex-direction: column;     justify-content: center;）

### 11.line-height的继承问题
- ①具体数值、直接继承
- ②比例，继承该比例
- ③百分比，如200%，先计算出来，再继承该值

### 12.rem是什么？响应式的常见方案有哪些？
&nbsp;&nbsp;&nbsp;&nbsp;rem：相对于根元素，是相对长度单位，常用于响应式布局

&nbsp;&nbsp;&nbsp;&nbsp;响应式的常见方案有哪些？
- 媒体查询
- flex 布局
- grid 布局（兼容性较差）
- 百分比布局
### 13.网页视图尺寸是什么？
- vh、vw，网页视口高度、宽度的1%


## 写在文末
&nbsp;&nbsp;&nbsp;&nbsp;如果你觉得我写得还不错的话，可以给我点个赞哦^^，如果哪里写错了、写得不好的地方，也请大家评论指出，以供我纠正。
## 其它文章
- [数据结构之二叉树的遍历](https://juejin.im/post/6854573218716958727)
- [仿去哪儿网项目视频学习总结](https://juejin.im/post/6850037271106682887)
- [Webpack简单学习](https://juejin.im/post/6844904187243790344)
- [面筋系列①——滴滴sp一面面试真题](https://juejin.im/post/6859957035321016328)
- [面筋系列②——滴滴实习生一面凉经](https://juejin.im/post/6863728442987741191)
