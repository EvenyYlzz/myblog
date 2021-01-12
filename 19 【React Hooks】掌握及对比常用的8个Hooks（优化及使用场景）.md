## 前言
&nbsp;&nbsp;&nbsp;&nbsp;有一段时间没写文章了，总结记录一下个人常用的或个人认为该掌握的九个`React Hooks`。我来网易之前是刚学的`React`框架，当时看了一天公司项目，然后发现得去看一下`React Hooks`，看了一个周末，做了点总结，在此分享一下。本文对每一个`Hooks`都结合了示例及使用场景进行讲解，稍微有点长，你可以针对你想要学习的`Hooks`有目的性得去看。
## React Hooks
### 1 useState
#### 1.1 类组件中
在类组件中，可以用`this.state`来定义类组件的状态，可以看下以下代码实现
```javascript
import React from 'react'

class StateClass extends React.Component{
    constructor(){
        super()
        this.state = {
            name: '类'
        }
    }

    render() {
        return (
            <div onClick={ this.setName }>
                这是一个类组件————{ this.state.name }
            </div>
        )
    }

    setName = () => {
        this.setState({
            name: '我通过类组件方法变成这样了'
        })
    }
}

export default StateClass
```

#### 1.2 函数组件中
在函数组件中，可以使用`useState`来定义函数组件的状态。使用`useState`来创建状态
- 1.引入
- 2.接收一个参数作为初始值
- 3.返回一个数组，第一个值为状态，第二个值为改变状态的函数

看一下同样的功能实现，函数组件的`useState`的代码实现如下。相同的功能，用函数组件的`useState`的写法是不是方便多了呢？接下来我们继续学习另外的`hooks`
```javascript
import React,{ useState } from 'react'

function StateFunction () {
    const [name, setName] = useState('函数')
    //     类名，修改函数名            初始值

    return (
        <div onClick={ () => setName('我使用hooks变成这样了') }>
        //	setName也可以写入方法，如setName（ val => val+'xxxx' ）
            这是一个函数式组件————{name}
        </div>
    )
}

export default StateFunction
```

### 2 useEffect
#### 2.1 简单介绍
`useEffect`又称副作用`hooks`。作用：给没有生命周期的组件，添加结束渲染的信号。执行时机：在渲染结束之后执行

- 什么是副作用？
  - 副作用 ( side effect ): 数据获取，数据订阅，以及手动更改 React 组件中的 DOM 都属于副作用
  - 因为我们渲染出的页面都是静态的，任何在其之后的操作都会对他产生影响，所以称之为副作用

- 使用：
  - 1.第一个参数，接收一个函数作为参数
  - 2.第二个参数，接收【依赖列表】，只有依赖更新时，才会执行函数
  - 3.返回一个函数，先执行返回函数，再执行参数函数
#### 2.2 不接受第二个参数的情况下
如果不接受第二个参数，那么在第一次渲染完成之后和每次更新渲染页面的时候，都会调用`useEffect`的回调函数，所以你要考虑好使用场景。
```javascript
import React,{ useEffect, useState } from 'react'

function StateFunction () {
    const [num, setNum] = useState(0)
    
    useEffect( () => {
        console.log('2222函数式组件结束渲染')
    })
    
    return (
        <div onClick={ () => setNum( num => num+1 ) }>
            这是一个函数式组件————{num}
        </div>
    )
}
```
#### 2.3 接受第二个参数的情况下
```javascript
useEffect( () => {
    console.log('2222函数式组件结束渲染')
},[])
//	改变useEffect第二个参数，其余代码同上
```
在这，我们可以对第二个参数传入一个数组，这个数组表示的是更新执行所依赖的列表，只有依赖列表改变时（当数组中的任意一项变化的时候，useEffect会被重新执行 ），才会触发回调函数
- 传入的为空数组`[]`，那么即告诉`useEffect`不依赖于`state`、`props`中的任意值，`useEffect`就只会运行一次，常用场景为页面获取数据的方法可以写入此处进行调用，以获取页面初始数据
- 传入一个值构建的数组、或者多个值构建的数组，如`[num]`、`[num,val]`，上述代码变更为如下。那么此时只有当数组中的值（任意一项即可）改变时，才会重新触发回调函数
  ```javascript
  useEffect( () => {
      console.log('2222函数式组件结束渲染')
  },[num])

  useEffect( () => {
      console.log('2222函数式组件结束渲染')
  },[num,val])
  //	改变useEffect第二个参数，其余代码同上
  ```
  
#### 2.4 清除副作用
上面写的都是一些不需要清除的副作用，只是回调触发一些简单的方法，但是有一些副作用是需要清除的。例如绑定一些`DOM`事件，在这种情况下，清除工作是非常重要的，可以防止引起内存泄露，例如下面给出的代码对比
- ①**未清除副作用的情况下**。此时第一次点击正常输出一次`打印当前位置`，而后每一次`useEffect`调用都会新绑定一个`updateMouse`方法，那么点击一次所触发绑定的方法越来越多，那么之后点击一次就会疯狂打印`打印当前位置`，这也就造成了页面性能、内存泄露等问题
  ```javascript
  const [positions,setPositions] = useState({ x:0,y:0 })
  
  useEffect( () => {
      console.log('2222函数式组件结束渲染')

      const updateMouse = (e) => {
          console.log('打印当前位置')
          setPositions({ x:e.clientX, y:e.clientY })
      }
      document.addEventListener('click',updateMouse)
  })
  
  return (
      <div>
          <p>x:{ positions.x }</p>
          <p>y:{ positions.y }</p>
      </div>
  )
  ```
- ②**清除副作用的情况下**（仅修改部分代码，其它代码同上）。例如示例代码
  - 首次刷新或进入页面会先执行除`return`以外的内容，也就是会执行一个绑定的方法，然后将`updateMouse`方法绑定到`click`事件上
  
  - 并将**改次**`useEffect`中的事件清除返回出去，但是此时是并没有执行`return`中的内容的（重点注意）
  
  - 然后当你点击第一次的时候，就会打印设置当前鼠标页面坐标，然后先执行上一次`return`返回出去的内容，注意这里是执行上一次`return`中的清除事件绑定方法，然后执行该清除事件绑定方法，当然清除也是清除的上一个`useEffect`中的绑定事件
  
  - 然后再开始执行新的`useEffect`中的绑定事件方法，并再次将改次`useEffect`清除事件绑定的方法`return`返回出去，如此就形成了一个链式一样的过程
  
  - 当页面卸载的时候，会执行最后一次`return`返回出来的清除事件绑定的方法，这样也就保证了页面卸载的时候，移除了绑定添加的`DOM`事件方法
  
  - （上述写的执行过程并没有从原理出发去分析的，只是简单的描述。可能稍微有点乱，如果你理解不了，可以多看几遍并动手执行示例代码结合进行理解）
  ```javascript
  useEffect( () => {
      console.log('2222函数式组件结束渲染')
      const updateMouse = (e) => {
          console.log('打印当前位置')
          setPositions({ x:e.clientX, y:e.clientY })
      }
      document.addEventListener('click',updateMouse) //  添加绑定方法事件(要修改依赖，绑定到依赖上)

      return () => {
          //  在每次执行useEffect之前都会执行上一次return中内容
          document.removeEventListener('click',updateMouse)
          //  移除绑定方法事件(要修改依赖，绑定到依赖上)
          console.log('1111销毁')
      }
  })
  ```
#### 2.5 useEffect中的异步
每个effect函数都属于一次特定的渲染：
- ①useEffect调度不会阻塞浏览器更新屏幕<异步>
- ②每次重新渲染都会生成新的effect，替换掉之前的，确保effect中获取的值是最新的，不用担心过期。如下，设置的`3000`毫秒内连续点击三次，那么将会一共打印4次，分别是`0、1、2、3`，`0`是第一次渲染结束之后自动触发的，剩下`1、2、3`则是点击三次每次触发时的`count`值
  ```javascript
  function Counter() {
    const [count, setCount] = useState(0);

    useEffect(() => {
      setTimeout(() => {
        console.log(`${count}`);
      }, 3000);
    });

    return (
      <div>
        <p>你点击了{count}次</p>
        <button onClick={() => setCount(count + 1)}>
          点击我
        </button>
      </div>
    );
  }
  ```
  - **与类组件进行对比**：如果放到类组件中，则是打印设置的时间内改变的最终值，在类组件中同等代码如下（仅给出关键代码）。打印设置的时间内改变的最终值是什么意思呢？如果你设置3000毫秒，那么渲染结束的瞬间开始计时，3000毫秒内连续点击三次，那么最终就会打印4次`3`，如果先等第一次`componentDidMount`中设置的定时器结束，再突然3000毫秒内连续点击三次，那么就会先打印第一次的`0`，再打印三次`3`，**因为类写法中是共用的同一个`num`状态值**。（如果你将时间设置为`0`毫秒，那么其实你连续点击三次，会跟`useEffect`一样，也是先打印一次`0`，再接着打印`1、2、3`，因为这个变化反应很快，你感觉不到差异，可以自己动手试试哦。）
    ```javascript
	this.state = {
      	num:0
    }
    
    componentDidMount(){
        setTimeout(() => {
            console.log(this.state.num)
        },3000)
    }

    componentDidUpdate(){
        setTimeout(() => {
            console.log(this.state.num)
        },3000)
    }
    
    render() {
        return (
            <div onClick={ this.setNum }>
                这是一个类组件————{ this.state.num }
            </div>
        )
    }
    
    setNum = () => {
        this.setState({
            num: this.state.num+1
        })
    }
    ```
### 3 useLayoutEffect
一般将`useLayoutEffect`称为有`DOM`操作的副作用`hooks`。作用是在`DOM`更新完成之后执行某个操作。执行时机：在`DOM`更新之后执行

与`useEffect`对比
- 相同点
  - 1.第一个参数，接收一个函数作为参数
  - 2.第二个参数，接收【依赖列表】，只有依赖更新时，才会执行函数
  - 3.返回一个函数，先执行返回函数，再执行参数函数
  - （所以说执行过程的流程是一样的）
- 不同点
  - 执行时机不同。`useLayoutEffect`在`DOM`更新之后执行；`useEffect`在`render`渲染结束后执行。执行示例代码会发现`useLayoutEffect`永远比`useEffect`先执行，这是因为`DOM`更新之后，渲染才结束或者渲染还会结束
 
```javascript
const [num, setNum] = useState(0)
//在类组件中用componentWillMount生命周期来实现
useLayoutEffect( () => {
    console.log('useLayoutEfffect')
	//	也可以在此进行事件绑定
    return () => {
    	//	也可以在此进行事件绑定移除
        console.log(1)
    }
},[num])

useEffect( () => {
    console.log('useEffect')
},[num])

return (
    <div onClick={ () => setNum( num => num+1 ) }>
        这是一个函数式组件————{num}
    </div>
)
```

### 4 useMemo
使用`useMemo`可以传递一个创建函数和依赖项，创建函数会需要返回一个值，只有在依赖项发生改变的时候，才会重新调用此函数，返回一个新的值。简单来说，作用是让组件中的函数跟随状态更新（即优化函数组件中的功能函数）。

- 使用：
  - 1.接收一个函数作为参数
  - 2.同样接收第二个参数作为依赖列表（可以与useEffect、useLayoutEffect进行对比学习）
  - 3.返回的是一个值。返回值可以是任何，函数、对象等都可以
#### 4.1 复杂计算逻辑优化使用场景
- 未优化前代码如下。当我们点击`div`区域时，此时触发的`setAge`，改变的是`age`，跟`getDoubleNum`方法其实是不相关的，但是如果你看下控制台，能看到打印了多次`获取双倍Num`，说明该方法不断被触发，其实是没必要触发的。如果方法内计算量大、对性能是有一定影响的，所以需要进行优化
  ```javascript
  const [num, setNum] = useState(1)
  const [age, setAge] = useState(18)

  function getDoubleNum () {
      console.log(`获取双倍Num${num}`)
      return 2 * num  //	假设为复杂计算逻辑
  }

  return (
    <div onClick={ () => { setAge( age => age+1 )} }>
        <br></br>
        这是一个函数式组件————{  getDoubleNum() }
        <br></br>
        age的值为————{ age }
        <br></br>
    </div>
  )
  ```
- 使用`useMemo`优化后代码如下。此时`getDoubleNum`方法是接收一个返回的值，所以要注意注释里所写的，括号是去掉了的。使用`useMemo`后，再点击`div`区域改变`age`的值，此时执行返回的`return 2*num`以及打印只有在`num`更新时才会去执行，然后返回值给到`getDoubleNum`再渲染到视图上，这样就减少了不必要的计算达到优化的作用
  ```javascript
  const [num, setNum] = useState(1)
  const [age, setAge] = useState(18)

  const getDoubleNum = useMemo( () => {
      console.log(`获取双倍Num${num}`)
      return 2 * num  //	假设为复杂计算逻辑
  },[num] )

  return (
      <div onClick={ () => { setAge( age => age+1 ) }  }>
          <br></br>
          这是一个函数式组件————num：{  getDoubleNum }  //  注意这里没括号，因为是返回值
          <br></br>
          age的值为————{ age }
          <br></br>
      </div>
  )
  ```
#### 4.2 父子组件重复渲染问题优化使用场景
- 未优化前代码如下。子组件包裹一个`memo`，但是包裹了还是会重新渲染, 为什么呢？因为我们定义的`info`是`const`定义的一个局部变量,每次重新渲染都是重新定义一个新的`info`，然后子组件进行浅层比较时候，`info`永远是不一样的，所以就会重新渲染（可以按照例子点击按钮，会发现子组件不断打印`我是子组件`）。如果子组件比较复杂的情况下，那么就会对页面性能产生影响
  ```javascript
  const Child = memo( () => {
      console.log('我是子组件')
      return <p>我是子组件</p>
  })
  
  function Parent() {
      const [show,setShow] = useState(true)

      const info = {
          name: 'Even',
          age: 22
      }

      return(
          <div>
              <Child info={ info } />
              <button onClick={ () => setShow(!show) }>点击更新状态</button>
          </div>
      )
  }
  ```
- 使用`useMemo`后代码如下（只给出修改代码，其它代码同上例子）。这样子优化后，子组件只会在初始化状态时渲染一次，当我们点击按钮时，因为`info`其包裹的`useMemo`依赖并没有改变，返回值是同一个值，所以不会造成子组件重新渲染。
  ```javascript
  const info = useMemo( () => {
      return {
          name: 'Even',
          age: 22
      }
  },[])
  ```
### 5 useCallback
`useMemo`讲完我们来讲一个跟其很相似的叫`useCallback`，作用也是让某些操作、方法跟随状态的更新而去执行。

与`useMemo`对比。
- 可以简单这样看作，`useMemo(() => Fn,deps)`相当于`useCallback(Fn,deps)`
不同点：
- `useCallback`是对传过来的回调函数优化，返回的是一个函数；`useMemo`返回值可以是任何，函数，对象等都可以
相同点：
- 在使用方法上，`useMemo`与`useCallback`相同。接收一个函数作为参数，也同样接收第二个参数作为依赖列表
#### 5.1 为何说`useCallback`缓存的是一个函数（重要区别）
- `useCallback`虽然与`useMemo`相似，但其返回及缓存的是一个函数，对比以下示例代码。先说①②③三种情况的对比（可以复制代码然后分别注释①②③代码对比）

  - 当①情况时，只会打印一次`获取双倍Num1`，也就是首次渲染的打印，之后再点击`div`区域改变`age`的值都与其无关，所以不会执行。因为`getDoubleNum`已经获得了`useMemo`中传入的函数执行后返回的值了，获取之后，便将其缓存下来了
  
  - 当②情况时，首次渲染会打印一次`获取双倍Num1`，然后每点击一次都会打印`获取双倍Num1`，这是为什么呢？不是说`useCallback`也有缓存的功能吗？这是因为我们前面提到的，`useCallback`返回的是一个函数。因为`useCallback`中的函数是在当前组件内定义的，组件重新渲染，它自然也会重新渲染，这又会有同学说了，可是这也不能说明它缓存的是一个函数啊。那么你可以先看看③的依赖为`[]`情况时，那么你就能明白了。**所以说复杂计算逻辑的场景不适合使用`useCallback`来缓存，因为传入的函数内容会不断执行**。
  
  - 当③情况时，我们结合②③处标记代码，`set`只能存入唯一值，我们观察打印的`set`的长度 
  
    - 当`useCallback`依赖为空`[]`时，我们连续多次点击`div`区域，虽然`useCallback`中的内容会不断执行，但是我们可以看到打印出来的`set`的长度一直都是`2`，这就是因为它不断将同一个函数添加进`set`，所以`set`的长度不变
    - 而当`useCallback`的依赖为`[num]`时，我们连续多次点击`div`区域，可以看到打印出来的`set`在不断累加，`1、2、3、4、5、6...`。因为`num`在改变，所以每一次缓存的函数都是一个新的函数，所以添加进`set`的函数是不一样的，所以`set`的长度点一次加一次
  ```javascript
  const set = new Set()
  export default function StateFunction () {
      const [num, setNum] = useState(1)
      const [age, setAge] = useState(18)

      const getDoubleNum = useMemo( () => {
          console.log(`获取双倍Num${num}`)
          return 2 * num  //	①假设为复杂计算逻辑
      },[] )

      const getDoubleNum = useCallback( () => {
          console.log(`获取双倍Num${num}`)
          return 2 * num  //	②假设为复杂计算逻辑
      },[] )

      set.add(getDoubleNum())  //	③注意set打印的长度变化（设置Callback的依赖为[]、[num]进行对比）
      console.log('set.size：',set.size)

      return (
          <div onClick={ () => { setNum( num => num+1 ) }  }>
              <br></br>
              这是一个函数式组件————num：{  getDoubleNum } //①useMemo情况下
              这是一个函数式组件————num：{  getDoubleNum() } //②useCallback情况下
              <br></br>
              age的值为————{ age }
              <br></br>
          </div>
      )
  }
  ```
#### 5.2 useCallback适用场景
可以对父子组件传参渲染的问题进行优化。简单来说就是，**父组件的传入函数不更新，就不会触发子组件的函数重新执行**
- 通常而言，父组件更新了，那么子组件也会更新。但是如果父组件传入子组件的内容不变，那么子组件某些操作（某些操作是**指需要跟随传入内容的改变而同步进行的操作**）是没必要执行的，这会影响页面性能，所以我们可以对这情况进行优化。

- 例如示例代码，我们将`getDoubleNum`传入子组件，此时点击`div`区域改变的是`num`的值，我们使用父组件`useCallback`配合子组件的`useEffect`来优化，只有当父组件的`num`改变导致传入子组件的`getDoubleNum`改变的时候，我们才会执行子组件某些需要更新的操作（即注释标注处代码），这样就可以避免子组件一些没必要的更新操作反复执行而影响页面性能
```javascript
function Parent () {

    const [num, setNum] = useState(1)
    const [age, setAge] = useState(18)

    const getDoubleNum = useCallback( () => {
        console.log(`获取双倍Num${num}`)
        return 2 * num
    },[num] )

    return (
        <div onClick={ () => {setNum( num => num+1 )} }>
            这是一个函数式组件————num:{  getDoubleNum() }
            <br></br>
            age的值为————age:{ age }
            <br></br>
            set.size:{set.size}
            <Child callback={ getDoubleNum() }></Child>
        </div>
    )
}

function Child(props) {
    useEffect( () => {
        console.log('callback更新了') //这里代表的是需要跟随传入内容的改变而同步进行的操作
    },[props.callback])

    return (
        <div>
            子组件的getDoubleNum{props.callback}
        </div>
    )
}
```
#### 5.3 总结及参考详解文章
简单总结使用场景判断：
- 在子组件不需要父组件的值和函数的情况下，只需要使用`memo`函数包裹子组件即可
- 如果有函数传递给子组件，使用`useCallback`
- 缓存一个组件内的复杂计算逻辑需要返回值时，使用`useMemo`
- 如果有值传递给子组件，使用`useMemo`
参考详解文章：
- 上述是我个人结合查阅资料的一些理解，如果你觉得我讲的还是不太清楚，可以参考下这两篇写得不错的文章，或许会更清晰
- [useMemo与useCallback使用指南](https://blog.csdn.net/sinat_17775997/article/details/94453167)
- [详解 useCallback & useMemo](https://juejin.cn/post/6844904101445124110)

### 6 useRef
简单来说`useRef`就是返回一个子元素索引，此索引在整个生命周期中保持不变。作用也就是：长久保存数据。注意事项，保存的对象发生改变，不通知。属性变更不会重新渲染
- 未使用`useRef`，如果我们有这样一个需求如下，需要当某个定时器自增的值达到限制条件后就清除该定时器，如下代码。此时以下的代码其实是没有办法完成给出的需求的，当`num`大于`10`后，会发现不停的打印`大于10了，清除定时器`，而其实是定时器没有清除掉的，所以会一直执行这两个打印内容，但是会发现打印出来的`timer`显示`undefined`，这是为什么呢？因为我们每次渲染都是通过`setInterval`重新返回的`timer`，`timer`也在更新，也就丢失了`timer`这个数据，导致无法准确清除某个需要清除的定时器
  ```javascript
  const [num, setNum] = useState(0)

  let timer
  useEffect( () => {
      timer = setInterval( () => {
          setNum( num => num+1 )
      },400 )
  },[] )

  useEffect( () => {
      if(num > 10){
          console.log('大于10了，清除定时器')
          console.log('timer：',timer)
          //  因为每一个timer都是独立render的，所以获取不到
          clearTimeout(timer)
      }
  },[num] )

  return (
      <div>
          这是一个函数式组件————num:{  num }
      </div>
  )
  ```
- 使用`useRef`后，代码如下。我们可以看到`num`自增到`11后`就打印了一次`大于10了，清除定时器`以及`ref.current 1`，然后就停止自增了，因为定时器被清除了。`ref`是一个对象，`ref.current`存储了该定时器在整个生命周期中的`id`值，所以当清除定时器的时候，可以准确清除这个定时器
  - 保存一个值，在整个生命周期中维持不变
  ```javascript
  const [num, setNum] = useState(0)

  const ref = useRef()
  useEffect( () => {
      ref.current = setInterval( () => {
          setNum( num => num+1 )
      },400 )
      // ref.current = '111'
  },[] )

  useEffect( () => {
      if(num > 10){
          console.log('大于10了，清除定时器')
          console.log('ref.current',ref.current)
          clearTimeout(ref.current)
      }
  },[num] )

  return (
      <div>
          这是一个函数式组件————num:{  num }
      </div>
  )
  ```
  - 重新赋值`ref.current`不会主动触发页面重新渲染。当我们将代码修改成下面这样，会在控制台打印发现`ref.current`的值打印为`111`，但是页面视图上显示的还是空，这是因为`ref`保存的对象发生改变，不会主动通知，属性变更不会重新渲染
  ```javascript
  const [num, setNum] = useState(0)

  const ref = useRef()
  useEffect( () => {
      ref.current = '111'
      console.log('ref.current',ref.current)
  },[] )

  return (
      <div>
      	  这是ref.current的值——ref.current:{ ref.current }
          <br></br>
          这是一个函数式组件————num:{  num }
      </div>
  )
  ```
  
### 7 useContext
`useContext`是让子组件之间共享父组件传入的状态的。作用通俗地说是带着子组件去流浪。
- 未使用`useContext`，我们有下列这样一个场景，我们父组件有传入一个值到不同的子组件中，示例给出的代码是`2`个这样的子组件，但是如果我需要添加的子组件特别多呢？总不能总是一个一个这样添加写入吧，而且如果传入的同一个变量名如果发生改变，还得一个个去改，所以我们可以用`useContext`优化一下代码
  ```javascript
  function StateFunction () {
      const [num, setNum] = useState(1)

      return (
          <div>
              <button onClick={ ()=> setNum(num => num+1) }>增加num的值+1</button>
              <br></br>
              这是一个函数式组件——num:{  num }
              <Item1 num={num}></Item1>
              <Item2 num={num}></Item2>
              //	......
          </div>
      )
  }

  function Item1 (props) {
      return (
          <div>
              子组件1 num：{ props.num }
          </div>
      )
  }

  function Item2 (props) {
      return (
          <div>
              子组件2 num：{ props.num }
          </div>
      )
  }
  ```
- 使用`useContext`优化后，代码如下，这样我们只需要在子组件中使用`useContext(Context句柄)`来获取数据即可，添加同类子组件时不需要再关注父组件中子组件定义时的`props`传入值，使用方法如下
  - 需要引入`useContetx`，`createContext`两个内容
  - 通过`createContext`创建一个context句柄
  - `Context.Provider`来确定数据共享范围
  - 通过`value`来分发内容
  - 在子组件中，通过`useContext(Context句柄)`来获取数据
  - **注意事项**，上层数据发生改变，肯定会触发重新渲染（点击`button`按钮触发父组件更新传入的`num`值能看到子组件重新渲染）
  ```javascript
  const Context = createContext(null)

  function StateFunction () {
      const [num, setNum] = useState(1)

      return (
          <div>
              <button onClick={ ()=> setNum(num => num+1) }>增加num的值+1</button>
              <br></br>
              这是一个函数式组件——num:{  num }
              <Context.Provider value={num}>
                  <Item3></Item3>
                  <Item4></Item4>
              </Context.Provider>
          </div>
      )
  }

  function Item3 () {
      const num = useContext(Context)

      return (
          <div>
              子组件3: { num }
          </div>
      )
  }

  function Item4 () {
      const num = useContext(Context)

      return (
          <div>
              子组件4: { num+2 }
          </div>
      )
  }
  ```
  
### 8 useReducer
以前是只能在类组件中使用`Redux`，现在我们可以通过`useReducer`在函数式组件中使用`Redux`。作用是可以从状态管理的工具中获取到想要的状态。
- 如何使用`useReducer`。`Redux`必须要有的内容就是仓库`store`和管理者`reducer`。而`useReducer`也是一样的，需要创建数据仓库`store`和管理者`reducer`，即示例代码注释处。然后我们就可以通过`①`处的定义一个数组获取状态和改变状态的动作，触发动作的时候需要传入`type`类型判断要触发`reducer`哪个动作，然后进行数据的修改。需要注意的地方是，在`reducer`中`return`的对象中，需要将`state`解构，否则状态就剩下一个`num`值了
  ```javascript
  const store = {
 	  age:18,
      num:1
  }	//	数据仓库

  const reducer = (state, action) => {
      switch(action.type){
          case 'add':
              return {
                  ...state,
                  num: action.num+1
              }

          default:
              return {
                  ...state
              }
      }
  } //	管理者

  function StateFunction () {
      const [state,dispacth] = useReducer(reducer,store)  //	①

      return (
          <div>
              <button onClick={ () => {
                  dispacth({
                      type: 'add',
                      num: state.num
                  })
              } }>
                  增加num的值+1
              </button>
              <br></br>
              这是一个函数式组件——num:{  state.num }
          </div>
      )
  }
  ```
## 总结
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;看完了吗？不知道你看完后是否有所收获，本文是从使用层面及一些日常应用示例上去分析的，并没有深层次剖析。如果你想要深层次了解掌握这几个`Hooks`，建议还是去看官方的源码哦。附上我的学习`Demo`仓库，[React Hooks学习仓库](https://github.com/EvenyYlzz/react-hooks-learning)
## 写在文末
&nbsp;&nbsp;&nbsp;&nbsp;如果你觉得我写得还不错的话，可以给我点个赞哦^^，如果哪里写错了、写得不好的地方，也请大家评论指出，以供我纠正
