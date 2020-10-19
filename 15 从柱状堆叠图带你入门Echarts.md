## 前言
实习一个多月了，在部门里负责了度量模块的页面编写，主要涉及的技术就是Echatrs。在此之前，我只稍微接触过python的数据可视化，并没有接触过Echarts。不过现在将度量模块的几个页面写下来，已经可以说是入门了，本文从图的展示需求着手，以柱状堆叠图的各种需求图为例，一步步带你们了解Echarts
## Echarts入门
### 1 安装
- ①输入`npm install echarts`安装
- ②然后在main.js中全局引入`import echarts from "echarts"`，`Vue.prototype.$echarts = echarts`，加入以上两行代码
### 2 如何在vue中使用echarts
#### 2.1 给定一块区域
在`<template></template>`中给定一块区域
```javascript
<div id="myChart1" :style="{width: '100%', height: '700px'}"></div>
```
#### 2.2 在methods中写入一个方法
```javascript
drawImg1() {
    // 基于准备好的dom，初始化echarts实例
    let myChart1 = this.$echarts.init(
        document.getElementById("myChart1")
    );
    // 绘制图表
    myChart1.setOption({

    });
},
```
setOption中写入官方的例子，官方的样例链接[官方柱状堆叠图](https://echarts.apache.org/examples/zh/editor.html?c=bar-y-category-stack)
[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-x53eAM3l-1603096099364)(https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8bab86088be64e0c94e1042c354b05a4~tplv-k3u1fbpfcp-watermark.image)]
直接复制过来，然后代码如下
```javascript
  drawImg1() {
        // 基于准备好的dom，初始化echarts实例
        let myChart1 = this.$echarts.init(
            document.getElementById("myChart1")
        );
        // 绘制图表
        myChart1.setOption({
			tooltip: {
            trigger: 'axis',
              axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                  type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
              }
            },
            legend: {
                data: ['直接访问', '邮件营销', '联盟广告', '视频广告', '搜索引擎']
            },
            grid: {
                left: '3%',
                right: '4%',
                bottom: '3%',
                containLabel: true
            },
            xAxis: {
                type: 'value'
            },
            yAxis: {
                type: 'category',
                data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日']
            },
            series: [
                {
                    name: '直接访问',
                    type: 'bar',
                    stack: '总量',
                    label: {
                        show: true,
                        position: 'insideRight'
                    },
                    data: [320, 302, 301, 334, 390, 330, 320]
                },
                {
                    name: '邮件营销',
                    type: 'bar',
                    stack: '总量',
                    label: {
                        show: true,
                        position: 'insideRight'
                    },
                    data: [120, 132, 101, 134, 90, 230, 210]
                },
                {
                    name: '联盟广告',
                    type: 'bar',
                    stack: '总量',
                    label: {
                        show: true,
                        position: 'insideRight'
                    },
                    data: [220, 182, 191, 234, 290, 330, 310]
                },
                {
                    name: '视频广告',
                    type: 'bar',
                    stack: '总量',
                    label: {
                        show: true,
                        position: 'insideRight'
                    },
                    data: [150, 212, 201, 154, 190, 330, 410]
                },
                {
                    name: '搜索引擎',
                    type: 'bar',
                    stack: '总量',
                    label: {
                        show: true,
                        position: 'insideRight'
                    },
                    data: [820, 832, 901, 934, 1290, 1330, 1320]
                }
            ]
        });
   },
```
上述代码，建议初学者还是在官方实例网站上多自己改改，观察图表变化。这样有助于理解，[官方柱状堆叠图](https://echarts.apache.org/examples/zh/editor.html?c=bar-y-category-stack)
#### 2.3 在mounted()挂载上面所写方法
```javascript
mounted(){
	this.drawImg1()
}
```
这样，我们的一个官方样图也就在页面渲染好了。
### 3 简单需求
需求的效果图如下
![](https://img-blog.csdnimg.cn/img_convert/c215f55ff6dbeb4e973d01f6ef93de63.png)
#### 3.1 分析需求
对比需求效果图跟官方样图的差异
- ①首先看到上面按钮，数据肯定得是根据用户选择来动态从后端获取再渲染绘制出图的
- ②x轴y轴方向与官方样图反过来
- ③主管增加需求：增加如图，能切换数据视图的按钮跟下载图片按钮
![](https://img-blog.csdnimg.cn/img_convert/6dde91387db1c0659d38ba34f4f94c1a.png)
- ④多了两根折线，并且折线要显示的是数据百分比，而不是直接显示数据
- ⑤柱状图的颜色
- ⑥需求效果图中左右两边都有y轴，也就是有两根y轴，并且柱状对应的是左边的y轴数据，折线对应的是右边的y轴数据
- ⑦柱状图以及折线图上数据的展示颜色
- ⑧主管增加需求：在横轴数据量过大的时候，可以放大缩小横轴，这样能清晰展示某一个区域的数据
#### 3.2 完成需求
这里可以看一下完成后的图如下
![](https://img-blog.csdnimg.cn/img_convert/38764f615186c059e18af2bd7eeb5d59.png)
具体完成过程以及踩坑过程，请看下面说明及代码中的注释。
- ①【**踩坑点**】获取动态数据时，切换不同选择按钮，然后重新从后端获取数据，重新渲染的时候，会发现有些数据不对，新的图中有的数据是从老的图里带过来的，这样也就导致了主管跟我说数据不对。我百度找啊找，才发现原来echarts有缓存功能，有的数据如果没有重新赋值是会保留下来带到下一个新的图然后继续渲染的。于是才发现要在`setOption`前加上`myChart1.clear();`这一行清除已绘制图表的代码
- ②【完成需求】按钮控制逻辑。我们可以用`Vue`的`watch`监听按钮值的变化，变化之后就执行重新从后端获取数据的方法再执行`drawImg1()`方法重新渲染
- ③【完成需求】x轴y轴方向与官方样图反过来。这个很容易解决，我们直接将官方样例的`xAxis`改成`yAxis`，`yAxis`改成`xAxis`这样就好了，具体逻辑可以自己尝试修改代码进行理解
- ④【完成主管增加需求】增加能切换数据视图的按钮跟下载图片按钮。这个echarts已经自带有这个封装功能，我们将代码增加写入即可，在下面代码中的`toolbox`
- ⑤【完成需求】多了两根折线，并且折线要显示的是数据百分比，而不是直接显示数据。这个也简单，我们只需要在`series`中增加两个控制折线的对象，在下面代码的`series`末尾中可以看到详细说明，至于展示数据显示为百分比，这个要用`formatter`进行控制
- ⑥【完成需求】柱状图的颜色。这个是可以在每一个`series`对象中单独通过`color`来设定控制，如果你不用的话，默认采用官方的柱状堆叠图实例的随机色
- ⑦【完成需求】需求图中左右两边都有y轴，也就是有两根y轴，并且柱状对应的是左边的y轴数据，折线对应的是右边的y轴数据。我们`yAxis`中写入两个对象，一个控制左边的y轴，一个控制右边的y轴，只需更改`position`的`left`或者`right`即可，这样就能有两根y轴了。这样之后我们还需要控制柱状对应的是左边的y轴，折线对应的是右边的y轴，因为`yAxis`里面放的是数组，默认对应第一个y轴，所以我们在`series`折线对象中加上`yAxisIndex: 1`这样就能对应上右边的百分比y轴，具体请看下面代码中末尾两个`series`对象的代码
- ⑧【完成需求】柱状图以及折线图上数据的展示颜色。这个在`series`的`label`的`textStyle`的`color`中写入即可
- ⑨【完成主管增加需求】在横轴数据量过大的时候，可以放大缩小横轴，清晰展示某一个区域的数据。加上如下代码中的`dataZoom`即可，具体控制逻辑在注释中说明
```javascript
 drawImg1() {
      // 基于准备好的dom，初始化echarts实例
      let myChart1 = this.$echarts.init(
          document.getElementById("myChart1")
      );
      myChart1.clear();  // ①此处为踩坑点，最好最好要加上这一行代码，以免动态切换图标带来的数据渲染错误
      // 绘制图表
      myChart1.setOption({
          title: { text: "定制版本" },  // 图表左上方的标题
          tooltip: {
              trigger: "axis",
              axisPointer: {
                  // 坐标轴指示器，坐标轴触发有效
                  type: "shadow", // 默认为直线，可选为：'line' | 'shadow'，自己切换再把鼠标放到数据视图上，能观察到指示器有所改变，一般还是建议采用shadow比较美观
              },
          },
          toolbox: {	// 此处则是控制右上角的按钮，能够切换数据展示视图，挺好用的，建议加上
              show: true,
              feature: {
                  // 控制是否出现数据视图，展示数据
                  dataView: {
                      show: true,
                      readOnly: false,
                  },
                  // 保存图片按钮，官方文档中还有其他按钮，可自行查看
                  saveAsImage: { show: true },
              },
          },
          legend: {
          	  // 此处控制上方图例，因为改图的图例定的比较死，所以我没用动态数据，直接在此写入
              data: [
                  "版本未申请发布量",
                  "发布成功数",
                  "发布失败数",
                  "发布成功率",
                  "申请率",
              ],
          },
          grid: {
              left: "3%",
              right: "4%",
              bottom: "10%", // 控制底部拖动条的位置，如果不改变这里，用默认的bottom值，拖动条会影响到数据视图
              containLabel: true,
          },
          yAxis: [
              {
                  type: "value",
                  position: "left",  // 左边，第一个y轴，控制展示位置
              },
              {
                  type: "value",
                  position: "right", // 右边，第二个y轴
                  axisLabel: {
                      formatter: "{value}%", // 控制y轴间隔展示数据的格式
                  },
                  splitLine: {
                      show: false, // 控制每一个间隔都是否有一条分割线
                  },
              },
          ],
          xAxis: {
              type: "category",
              data: this.Img1xAxisData, // 动态使用data中的数据，先用获取数据方法获取数据存入到data中，再调用drawImg1()渲染
          },
          //  dataZoom为控制是否可以进行数据放大放小
          dataZoom: [
              {
                  type: "inside",  // 加上这一个inside，即可以将鼠标放在数据视图上，然后用鼠标的滚轮滚动上下进行x轴的放大与缩小，具体区别可以自己删了跟增加这一行代码对比。如果没有这一行，那么就只能通过滚动下方拖动条来控制x轴上下
              },
              {
                  start: 0,
                  end: 10,
                  handleIcon:
                      "M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z",
                  handleSize: "80%",
                  handleStyle: {
                      color: "#fff",
                      shadowBlur: 3,
                      shadowColor: "rgba(0, 0, 0, 0.6)",
                      shadowOffsetX: 2,
                      shadowOffsetY: 2,
                  },
              }, //	这个控制拖动条的样式，一般这个不需更改
          ],
          series: [
              {
                  name: "版本未申请发布量",  //	name会对应图例进行控制，不能够乱填，后期也应该做成动态的
                  type: "bar", // 柱状图的柱子，line则为折线
                  stack: "总量",
                  color: ["#ff9933"], // 控制柱子颜色
                  label: {
                      show: true,  // 控制展示数据在柱子的展示与否
                      position: "inside", // 控制战术数据在柱子的哪个位置，可以是inside、top、insideRight
                      textStyle: {  // 控制柱子中数据的格式
                          // fontWeight: "bolder", // 加粗
                          fontSize: "12", // 字体大小
                          color: "#141414", // 字体颜色
                      },
                  },
                  data: this.Img1unpublishversion,
              },
              {
                  name: "发布成功数",
                  type: "bar",
                  stack: "总量",
                  color: ["#ffff99"],
                  label: {
                      show: true,
                      position: "inside",
                      textStyle: {
                          // fontWeight: "bolder",
                          fontSize: "12",
                          color: "#141414",
                      },
                  },
                  data: this.Img1successversion,
              },
              {
                  name: "发布失败数",
                  type: "bar",
                  stack: "总量",
                  color: ["#3333ff"],
                  label: {
                      show: true,
                      position: "inside",
                      textStyle: {
                          // fontWeight: "bolder",
                          fontSize: "12",
                          color: "#141414",
                      },
                  },
                  data: this.Img1failversion,
              },
              {
                  name: "发布成功率",
                  type: "line", // 折线
                  stack: "", // 此处要为空，不能对应总量
                  symbolSize: 10, // 拐点大小
                  yAxisIndex: 1, // 对应第二根y轴
                  lineStyle: {
                      // color: "#66cc33", //改变折线颜色
                      width: 2， // 改变折线粗细
                  },
                  itemStyle: {
                      normal: {
                          label: {
                              show: true, // 折线图展示数字与否
                              formatter: "{c}%", // 自定义数据值加百分比，控制展示数据格式
                          },
                      },
                  },
                  data: this.Img1successpercent,
              },
              {
                  name: "申请率",
                  type: "line",
                  stack: "",
                  symbolSize: 10, //拐点大小
                  yAxisIndex: 1,
                  lineStyle: {
                      color: "#3366cc",
                      width: 2,
                      //改变折线颜色
                  },
                  itemStyle: {
                      normal: {
                          label: {
                              show: true,
                              formatter: "{c}%", // 自定义数据值加百分比
                          },
                      },
                  },
                  data: this.Img1failpercent,
              },
          ],
      });
  },
```
看完上面的代码及注释说明，你应该能够掌握基础的用法了
### 4 需求进阶
其实真正难的还在后面，因为上面数据还很少，我们的对象是手动写入的，但如果我们的对象特别多，难道你要在`series`中一个个将对象添加进去吗？而且每一次获取新的数据，`series`的对象也是不一样的，所以很显然我们需要一个动态的`series`。我这里就不放新的需求图是怎样的了，直接放完成的图。就像下面这个图，每一条柱子上是特别多的数据的，每多一个图例数据，我们就要多一个`series`，所以我们需要动态写入`series`
![](https://img-blog.csdnimg.cn/img_convert/3ad43e852841c3d24c3edfa0809399ec.png)
#### 4.1 分析需求
对比这个新的图跟上面的第一个完成图又有哪些不一样的地方
- ①图例、每一根柱子上的对象特别多
- ②左边y轴的文字是蓝色的，并且离数据视图有一定小小的距离
- ③图例在数据视图的下方
- ④每一根柱子的最右边有一个总计数值
- ⑤`dataZoom`在右边
#### 4.2 完成需求
- ①【完成需求、踩坑点】不能一个个写入，因为是动态的，所以要写一个方法动态将`series`加入。这里我一开始想了好久也没想到怎么处理，后来才想到我直接根据后端获取的数据存到`data`中，然后我在`computed`写一个方法，根据`data`中获取的数据，动态返回一个`series`对象数组，然后再渲染到数据视图中，具体在下面代码的`computed`中查看
- ②【完成需求】左边y轴的文字是蓝色的，并且离数据视图有一定小小的距离。在`yAxis`中加入`axisLabel`，然后用`textStyle`来控制文字样式，`marigin`来控制文字离数据视图的距离
- ③【完成需求】图例在数据视图的下方。在`legend`中加入`y: "bottom"`
- ④【完成需求】每一根柱子的最右边有一个总计数值，添加一个计算总量的`series`对象，具体在下面代码的`computed`中查看。具体不同的实现方法参考[echarts 堆叠柱状图顶部显示总和](https://blog.csdn.net/lxj7607/article/details/100710955)
- ⑤【完成需求】`dataZoom`在右边。因为在这图里，x轴放到了左边，也就是变成了y轴，所以我们在`dataZoom`中加入两行`yAxisIndex: 0`，这样滚动条即可对应到左边的y轴，具体请看下列代码及注释

```javascript
   drawImg2() {
      // 基于准备好的dom，初始化echarts实例
      let myChart2 = this.$echarts.init(
          document.getElementById("myChart2")
      );
      myChart2.clear();
      // 绘制图表
      myChart2.setOption({
          tooltip: {
              trigger: "axis",
              axisPointer: {
                  // 坐标轴指示器，坐标轴触发有效
                  type: "shadow", // 默认为直线，可选为：'line' | 'shadow'
              },
          },
          toolbox: {
              show: true,
              feature: {
                  //控制是否出现数据视图
                  dataView: {
                      show: true,
                      readOnly: false,
                  },
                  saveAsImage: { show: true },
              },
          },
          legend: {
              data: this.Img2Legend, // 动态获取从后端获得的图例数据
              y: "bottom", // 控制图例在下方位置
          },
          grid: {
              left: "3%",
              right: "4%",
              bottom: "10%", //    控制底部拖动条的位置
              containLabel: true,
          },
          xAxis: [
              {
                  type: "value",
                  position: "left",
              },
          ],
          yAxis: {
              type: "category",
              data: this.Img2xAxiasData,
              axisLabel: {
              	  // interval: 0,
                  // rotate: 30, 这里这两个可以控制文字倾斜度
                  textStyle: {
                      color: "#3399ff",
                  }            // 坐标轴刻度的样式
                  margin: 20, // 控制文字离数据视图的距离
              },
          },
          dataZoom: [
              {
                  type: "inside",
                  yAxisIndex: 0, // 这里图里在右边，要对应的是左边的y轴竖轴
              },
              {
                  start: 0,
                  end: 10,
                  handleIcon:
                      "M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z",
                  handleSize: "80%",
                  handleStyle: {
                      color: "#fff",
                      shadowBlur: 3,
                      shadowColor: "rgba(0, 0, 0, 0.6)",
                      shadowOffsetX: 2,
                      shadowOffsetY: 2,
                  },
                  yAxisIndex: 0, // 这里图里在右边，要对应的是左边的y轴竖轴
              },
          ],
          series: this.Img2series, // 动态添加series对象，具体请看下面的代码
      });
  },
```
上面是该数据视图的方法，具体的`series`动态数据代码在`computed`中，请查看下列代码
```javascript
computed:{
	Img2series: function () {
        let Arr = [];
        let Img2sumData = [];
        let sum = 0;
        if (this.Img2yAxiasEndData.length) {
        	// 在y轴有数据的情况下，如果还有可能报错，要在再多加一层if(this.Img2yAxiasEndData)判断，然后再进行统计每一根柱子加起来的总值
            for (let j = 0; j < this.Img2yAxiasEndData[0].length; j++) {
                sum = 0;
                for (let i = 0; i < this.Img2yAxiasEndData.length; i++) {
                    sum = sum + this.Img2yAxiasEndData[i][j];
                }
                Img2sumData.push(sum);
            }
        }
        //	添加总计值
        Arr.push({
            name: "总计",
            type: "bar",
            barGap: "-100%", // 这里是关键，因为这个总计其实是多加一个透明的柱状图对象叠加在原来的图上
            label: {
                normal: {
                    show: true,
                    position: "right",
                    formatter: "总计{c}",
                    textStyle: { color: "#000" },
                },
            },
            itemStyle: {
                normal: {
                    color: "rgba(128, 128, 128, 0)", //	透明的
                },
            },
            data: Img2sumData,
        });
        if (this.Img2Sixvalue === "按审计码") { // 按钮控制逻辑
        	//	遍历添加series
            for (let i = 0; i < this.Img2Legend.length; i++) {
                Arr.push({
                    name: this.Img2Legend[i], // 对应第几个图例
                    type: "bar",
                    stack: "总量",
                    label: {
                        show: true,
                        position: "inside",
                        textStyle: {
                            fontSize: "12",
                            color: "#141414",
                        },
                    },
                    data: this.Img2yAxiasEndData[i], //	对应第几组数据
                });
            }
        } else {
            this.Img2Legend.push("总量");
            Arr.push({
                name: "总量",
                type: "bar",
                stack: "总量",
                color: ["#3399ff"],
                label: {
                    show: true,
                    position: "inside",
                    textStyle: {
                        fontSize: "12",
                        color: "#141414",
                    },
                },
                data: Img2sumData,
            });
        }
        return Arr; // 将这个数组返回出去，那么drawImg2()中的series就能渲染上去了
    },
}
```
## 总结
&nbsp;&nbsp;&nbsp;&nbsp;用了Echarts之后，真心感觉Echarts是一款很强大的可视化图标库，就像年初的疫情的疫情地图，也是通过Echarts进行展示的，很直观的一种展示方式，我自己也做过一个，如图[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-zeEAVdiH-1603096099373)(https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9004a456e58b4f7d8aaba105ebcca9c6~tplv-k3u1fbpfcp-watermark.image)]
&nbsp;&nbsp;&nbsp;&nbsp;本篇文章，只能够将你领入门而已，想要真正玩转Echarts，还是要自己看官方文档，根据实例不断尝试修改
## 写在文末
&nbsp;&nbsp;&nbsp;&nbsp;如果你觉得我写得还不错的话，可以给我点个赞哦^^，如果哪里写错了、写得不好的地方，也请大家评论指出，以供我纠正。（这个系列也快完结了，大家可以复习复习前面的，接下来我会更新一些别的文章，也请大家可以继续关注哦）
## 其它文章
- [前端一面基础知识 ⑥——性能优化、Web安全、Linux常用命令](https://juejin.im/post/6881790461841768455)
- [前端一面基础知识 ⑤——Http、Ajax、跨域](https://juejin.im/post/6878455733131116551)
- [前端一面基础知识 ④——事件与DOM](https://juejin.im/post/6877461224708112397)
- [前端一面基础知识 ③——异步（面试场景题）](https://juejin.im/post/6874852295348060167)
- [前端一面基础知识 ②——作用域和闭包（面试场景题）](https://juejin.im/post/6869272556654821389)
- [前端一面基础知识 ①——CSS面试题](https://juejin.im/post/6865946058795515918)
