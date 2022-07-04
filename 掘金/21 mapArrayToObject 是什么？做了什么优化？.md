---
theme: orange
highlight: atom-one-dark-reasonable
---
## 前言
&nbsp;&nbsp;&nbsp;&nbsp;上周，我在整理部门内的系统使用多次的函数时，我发现了这样一个函数，它的的名字叫`mapArrayToObject`。当时同事给我讲的是，这个函数将一些处理数据的方法的算法时间复杂度从`n^2`降到了`n`，可是光从字面上理解感受并不深，于是在我实际操作一番体会过后，我把我体会的过程整理写成了本文

## 正文
### 1.What is it?What it do?
- 只看`mapArrayToObject`这个函数的名称，能知道它其实就是将数组`Array`转为了`Object`
- `mapArrayToObject`其实就是将一个类型为`Array<object>`的数组，转化为以给定的`key参数`为key值的`Object`

### 2. 函数展示
```js
export function mapArrayToObject(arr = [], key) {
  return arr.reduce((pre, next) => {
    pre[next[key]] = next;
    return pre;
  }, {});
}
```
本文相应Demo链接 👉 [mapArrayToObject Demo](https://github.com/EvenyYlzz/workProblem-demos/tree/master/mapArrayToObject-Demo)

### 3. 使用方法示例
如下，将`testArr`数组通过`mapArrayToObject`转化为了以`id`为`key`值的`Object` (newTestObject)

```js
let testArr =
[
  {
    id: '11111',
    name: 'zhangsan',
  },
  {
    id: '22222',
    name: 'lisi',
  },
  {
    id: '33333',
    name: 'wangwu',
  },
]

// 使用mapArrayToObject处理后
let newTestObject = mapArrayToObject(testArr, 'id')

// console.log(newTestObject)
{
  11111: {
    id: '11111',
    name: 'zhangsan',
  },
  22222: {
    id: '22222',
    name: 'lisi',
  },
  33333: {
    id: '33333',
    name: 'wangwu',
  },
}
```

### 4. 使用场景示例
以 [demo](https://github.com/EvenyYlzz/workProblem-demos/tree/master/mapArrayToObject-Demo) 中的例子来展开讲，如下：

```js
<button id="btn2">
  按钮二
</button>

let arr = []
let list = []
// 生成200条假数据
for (let i = 0; i<200; i++) {
    arr.push({
      id: `index${i}`,
      height: 11,
      width: 22,
    })
    list.push({
      id: `index${i}`,
      margin: 11,
      padding: 22,
    })
}

let btn2 = document.getElementById('btn2')
btn2.addEventListener('click', () => {
    // 【关键代码 1 处】
    const newObject = mapArrayToObject(arr, 'id')
    list.map(i => {
      return {
        ...i,
        // 【关键代码 2 处】
        item: newObject[i.id],
      }
    })
});
```

- 在该方法中，将请求回来的数据，通过`mapArrayToObject`转化后生成以`id`为`key`值的`Object`(关键代码 1 处的`newObject`)
- 然后在`list`的`map`循环中（关键代码 2 处），直接通过`newObject`取到对应`id`的对象

### 5. 从算法的时间复杂度分析

#### 5.1 常规情况，不使用 mapArrayToObject（以上面第 4 点的例子为例）

```js
let btn1 = document.getElementById('btn1')
btn1.addEventListener('click', () => {
    console.time('click1')
    list.map(i => {
      return {
        ...i,
        // 【关键代码】
        item: arr.find(k => k.id === i.id),
      }
    })
    console.timeEnd('click1')
});
```

- `list`中的`map`循环为`n`
- `arr`中的`find`循环为`n`
- 所以总时间复杂度为`n * n = n^2`

#### 5.2 使用 mapArrayToObject

```js
let btn2 = document.getElementById('btn2')
btn2.addEventListener('click', () => {
    console.time('click2')
    // 【关键代码 1 处】
    const newObject = mapArrayToObject(arr, 'id')
    list.map(i => {
      return {
        ...i,
        // 【关键代码 2 处】
        item: newObject[i.id],
      }
    })
    console.timeEnd('click2')
});
```

- `mapArrayToObject`方法中的`reduce`循环复杂度为`n`
- `list`中的`map`循环复杂度为`n`
- 直接通过`newObject[i.id]`取到对应`id`的对象，复杂度为`1`，可忽略
- 所以总时间复杂度为`n + n = 2n`，在常数可忽略的情况下，也就是`n`

#### 5.3 分析总结

从时间复杂度的分析来看，在该示例中，如果在数据量足够大的情况下，使用`mapArrayToObject`会大大优化方法中对数据处理的速度

### 6. 不同数据量的优化前后时间对比

#### 6.1 小数据量情况下的对比结果

以`5.1、5.2`的例子中不使用`mapArrayToObject`和使用`mapArrayToObject`对数据的处理，我进行了一个时间上的对比，对比结果如下：

- 当数据量为 20 条时

  - 不使用 mapArrayToObject 的 5 次测试记录时间分别为： 0.09、0.12、0.10、0.10、0.09 （单位为 ms），平均值为：0.1ms
  - 使用 mapArrayToObject 的 5 次测试记录时间分别为： 0.12、0.04、0.14、0.08、0.11 （单位为 ms），平均值为：0.098ms
  - 使用`mapArrayToObject`平均快`0.002ms`，几乎可忽略


- 当数据量为 50 条时

  - 不使用 mapArrayToObject 的 5 次测试记录时间分别为： 0.55、0.32、0.24、0.21、0.31（单位为 ms），平均值为：0.326ms
  - 使用 mapArrayToObject 的 5 次测试记录时间分别为： 0.09、0.04、0.16、0.14、0.17（单位为 ms），平均值为：0.12ms
  - 使用`mapArrayToObject`平均快`0.206ms`，几乎可忽略

- 当数据量为 100 条时

  - 不使用 mapArrayToObject 的 5 次测试记录时间分别为： 0.76、0.71、0.92、0.59、0.94（单位为 ms），平均值为：0.784ms
  - 使用 mapArrayToObject 的 5 次测试记录时间分别为： 0.18、0.32、0.29、0.27、0.21（单位为 ms），平均值为：0.254ms
  - 使用`mapArrayToObject`平均快`0.53ms`，几乎可忽略

- 当数据量为 200 条时

  - 不使用 mapArrayToObject 的 5 次测试记录时间分别为： 1.04、0.99、1.33、2.11、1.52（单位为 ms），平均值为：1.398ms
  - 使用 mapArrayToObject 的 5 次测试记录时间分别为： 0.25、0.14、0.41、0.36、0.31（单位为 ms），平均值为：0.294ms
  - 使用`mapArrayToObject`平均快`1.104ms`，几乎可忽略

可以看到其实在数据量小的情况下，这个优化速度并不明显，是可以忽略的，那我们再看看数据量大的情况

#### 6.2 大数据量情况下的对比结果

- 当数据量为`1000`条时
  - ![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/24be83e98a1b43bdbd707660aac475bb~tplv-k3u1fbpfcp-watermark.image?)
  - 使用`mapArrayToObject`平均快`16.258ms`

- 当数据量为`10000`条时

  - ![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb317d5cc02849f4b98b73c26ef21b75~tplv-k3u1fbpfcp-watermark.image?)
  - 使用`mapArrayToObject`平均快`279ms`
  
- 当数据量为`100000`条时
  - ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/51dd075c12284b74bbf9a46ce4fb0217~tplv-k3u1fbpfcp-watermark.image?)
  - 使用`mapArrayToObject`平均快`24240.6ms`
#### 6.3 对比结果总结
在数据足够大的时候，使用`mapArrayToObject`处理数据的速度就会明显快很多很多。以上`demo`中的例子，所操作数据的数据结构还较为简单，实际在业务系统使用中，当数据结构足够复杂时，两种处理方式上的速度差异会更明显。并且受不同设备的`Cpu`处理速度的影响，这个差异也会不一致，所以在类似这种场景中，尽量使用`mapArrayToObject`函数还是很有作用的

## 总结反思
- 虽然我们业务系统中理论上是不会存在一次性操作这么大量的数据的情况，但是我们如果我们有与示例类似的操作场景下，可以尽量使用`mapArrayToObject`来进行操作
- 在处理数据时，使用`mapArrayToObject`也会更加便捷，想要某条数据可以直接通过对应`key`值去获取
- 在`codeReview`时应注重`review`一些数据操作的算法复杂度，虽然能优化提高的时间不多，但从代码分析算法复杂度本身也是对`review`成员的一种自我提升
