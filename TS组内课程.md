- 类型简单介绍
- 强调类型讲述更完善
- 怎么使用、例子更完善
- 理解层面

### 开发环境搭建
- 1.什么是ts
  - js超集，ts包含es5、es6、7、8，包含实验阶段语法
- 2.主流浏览器无法直接运行ts、需要借助编译器转成js版本
- 3.优点
  - 类型检查、类型推演、类型匹配
  - 避免低级错误
  - 等等
- 4.安装a
  - 运行`npm install -g typescript`命令进行安装
  - 运行`tsc -v`查看ts的编译器版本，正常显示版本即说明安装成功
- 5.demo运行操作
  - 新建ts文件，编写demo例子代码
  - 控制台使用`tsc main.ts`编译`ts文件`
  - 然后会生成对应文件名的js文件，如生成`main.js`
  - 然后运行`node main.js`文件即可查看实际运行效果

- 目的

### 1 基本类型

#### Boolean、Number、String
- 自动映射数据类型(类型推导补充)
  - ```js
    let bool1 = true
    let number1 = 1
    let string1 = 'aaa'
    ```
- 手动指定类型
  - ```js
    let bool2: boolean = true
    let number2: number = 1
    let string2: string = '1'
    ```
- 此时重新赋值给定不匹配的类型的则会报错，在编写阶段即报错
  - ```js
    let bool3: boolean = false
    bool3 = 'bbb' // 报错，定义时已经指定了类型，不能将类型“string”分配给类型“boolean”
    ```
#### Array
- 数组中可以存放任意类型的数据
- 直接创建，自动映射数据类型
  - ```js
    let arr1 = [1,2,3,4] // let arr1: number[]
    
    // 混合类型
    let arr2 = [1, 'ddd'] // let arr2: (string | number)[] 数字或字符串类型
    ```
- 手动指定数组内类型
  - ```js
    let arr3: number[] = [1,2,3,4]

    let arr4: (string|number)[] = [1, 'ddd'] // ｜ 或
    
    let arr5: any[] = [1, 'ddd', true, null]
    ```
- 利用泛型创建数组，在此处可仅作为了解，后续要再深入
  - ```js
    // 在泛型的Array中填充每一个元素为number类型
    let arr6: Array<number> = [1,2,3,4]
    ```
- 修改不符合数组给定类型的值，或添加不匹配类型的值，会报错
  - ```js
    let arr7: number[] = [1,2,3,4]
    
    // 不能将类型“boolean”分配给类型“number”。ts(2322)
    arr7[1] = true

    // 类型“string”的参数不能赋给类型“number”的参数。ts(2345)
    arr7.push('5')
    ```

#### Object
- 直接创建，自动映射类型
  - ```js
    const person1 = {
      firstName: 'alex',
      lastName: 'Even',
      age: 18
    }
    
    // const person1: {
    //   firstName: string;
    //   lastName: string;
    //   age: number;
    // }
    // 此时已经自动匹配了对应类型了
    ```
- 显式地定义属性类型
  - ```js
    const person2: {
      firstName: string,
      lastName: string,
      age: number,
    } = {
      firstName: 'alex',
      lastName: 'Even',
      age: 18
    }
    ```
    
- 访问未定义属性会报错
  - ```js
    const person1 = {
      firstName: 'alex',
      lastName: 'Even',
      age: 18
    }
  
    // 在原生js中，如此调用是不会报错的
    // 但是在ts中，调用一个未定义的内部变量，会直接报错
    // 类型“{ firstName: string; lastName: string; age: number; }”上不存在属性“what”。
    console.log('person1.what', person1.what)
    ```
- 尽量避免使用`object`、`Object`
  - 使用关键字`object`、`Object`定义类型，会有一些不可避免的错误，一般也比较少用，常用接口`Interfaces`，例如：
    ```js
    const person3: object = {
      firstName: 'alex',
      lastName: 'Even',
      age: 18
    }

    // 此时这里会报错，因为给定object，此时会认为是给定一个空的 {}，不确定内部情况
    // 所以在ts中，object这个关键词使用到的几率很小
    console.log('person3.firstName', person3.firstName)
    ```
- 不常用、但需要注意区分`object`、`Object`、`{}`
  - ```js
    let person5: Object
    person5 = '111'

    let person6: object
    person6 = '111' // 不能将类型“string”分配给类型“object”。ts(2322)

    let person7: {}
    person7 = '111'
    ```
  - 1. `object`是`ts 2.2`中引入的新类型，用于表示非原始类型。ts中的原始类型有: `string，number，symbol，bigint，undefined，null，boolean`。原始类型不能赋值给 `object` 类型的变量
  - 2. `Object`表示包含`toString, hasOwnProperty`等方法的`JavaScript`对象。所有的值（包含原始类型和非原始类型）都可以赋值给`Object`类型的变量
  - 3. `{}`表示空对象，基本上跟`Object`类型是一样的。可以使用`Object`中`prototype`上面的方法，可以将原始类型赋值给`{}`类型的变量


#### Symbol
- 直接创建
  - ```js
    const s1 = Symbol()
    const s2 = Symbol()

    console.log(s1) // Symbol()
    console.log(typeof s1) // symbol
    console.log(s1 === s2) // false 恒为false
    ```
- 显式指定类型创建
  - ```js
    const s3: symbol = Symbol()
    const s4: symbol = Symbol()

    console.log(s3) // Symbol()
    console.log(typeof s3) // symbol
    console.log(s3 === s4) // false 恒为false
    ```
- Symbol.for
  - ```js
    const s5: symbol = Symbol.for('foo')
    const s6: symbol = Symbol.for('foo')

    // Symbol.for会去全局查找是否已经存在对应标识符对应的symbol值
    // 如果存在，直接返回，如果不存在，则新建一个symbol值
    console.log(s5 === s6) // => true
    ```
#### null 和 undefined
- 直接创建undefined、null
  - ```js
    let x = null
    let y = undefined
    // 但其实这样创建的会被编译器识别为any类型，悬浮变量可以看到let x: any、let y: any
    // 因为是let创建的，是可以被重新赋值的，所以为any
    
    const xx = null
    const yy = undefined
    // 这样直接创建才会被识别为undefined类型，const xx: null、const yy: undefined
    ```
- 显式指定
  - ```js
    let z: null = null
    let u: undefined = undefined
    ```

#### void
void是空值的意思，在js中并没有这个空值的概念，在ts中用void来表示空值
- void的创建
  - ```js
    // 悬浮函数名称看到function printResult(): void
    // 说明这个函数的返回值为void
    function printResult1() {
      console.log('ddd1')
    }

    ```
- 指定返回空值类型
  - ```js
    // 这样也是可以的，但其实在Js中并不存在void类型
    // 所以当编译为js文件时，会发现其实打印了一个undefined
    function printResult2(): void {
      console.log('ddd2')
    }
    ```
- 声明一个`void`类型的变量没有什么用，因为你只能将它赋值为`undefined`（`null`不行）
  - ```js
    let void1: void
    void1 = undefined

    let void2: void = undefined
    ```

#### any
- 创建一个any值
  - 变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型
    ```js
    let something
    something = 'seven'
    something = 7

    something.setName('Tom')
    ```
- 指定any值
  - ```js
    let anyValue: any = 666
    anyValue = true
    anyValue = 'ddd'
    anyValue = {}

    anyValue() // 运行调用时才会报错，这就有点像JS
    
    // toUpperCase是string类型的方法，但这在编写阶段也没有报错提示，正是因为它是any
    anyValue.toUpperCase()
    ```
- 为什么ts还引入any
  - 1.加速开发过程，避免太冗长，没必要的类型定义
  - 2.对于Any，应该是要正确去看待
    - 语言只是一种实现工具，如果为了不必要的规范降低了开发效率，带来的收益还小，那么我们宁可不遵守这些不必要的规范
    - 而JS之所以可以这么多年经久不衰，也正是因为它的灵活性。而令人头疼的也是JS的灵活性，带来一些不可控的bug，所以结合ts的any，应视情况使用
    - ts引入any，也是为了保留一定的JS灵活性，存在就肯定是有意义的
    - 所以对ts的规范，如何定义一个正确的边界应该在实践中去总结，不断摸索出一套属于团队的规范

#### unknown
跟any相似。unknown虽然不保证类型，但会保证类型的安全
- ```js
  let unknownValue: unknown = 666

    unknownValue = true
    unknownValue = 'ddd'
    unknownValue = {}
    
    // 也就是，使用unknown，需要做一定的判断，或者类型判断，确定了类型之后，才能正常使用它的方法、或者调用

    // 下面两行代码会直接报错
    unknownValue()
    unknownValue.toUpperCase()

    // 做类型判断后就不会报错
    if (typeof unknownValue === 'function') {
      unknownValue()
    }

    if (typeof unknownValue === 'string') {
      unknownValue.toUpperCase()
    }
  ```

#### never
never使用场景
- 中途就抛出异常的函数
  ```js
  // 悬浮该函数会看到 function throwError1(message: string, errorCode: number): void
  // 但void在这其实是不准确的，请看下面，因为函数永远直接抛出错误，并不能执行完
  function throwError1(message: string, errorCode: number) {
    throw {
      message,
      errorCode
    }
    /////
  }

  throwError1('not found1', 404)


  function throwError2(message: string, errorCode: number): never {
    throw {
      message,
      errorCode
    }
    // 函数在上面会直接拦截抛出错误，是执行不到这里的，所以正确返回类型应该是never
  }
  
  throwError2('not found2', 505)
  ```
- 死循环函数
  ```js
  // 如果是死循环也是会让函数无法执行完成的，使用never
  function whileLoop(): never {
    while(true) {
      console.log('true')
    }
  }
  ```
- 区别于void
  - 当一个函数返回空值时，它的返回值为 void 类型，但是，当一个函数永不返回时（或者总是抛出错误），它的返回值为 never 类型

#### 元组
JavaScript 并不支持元组，开发者们通常只能使用数组来表示元组
- 创建元组
  ```js
  let person1: [number, string] = [1, 'ddd']

  person1[0] = 'aaa'
  person1[1] = 1
  person1[2] = 2
  // 以上3个赋值都是错误的
  person1.push(3) // push推入符合的类型，不会报错
  person1.push(null) // 但是不符合的类型是会报错的,类型“null”的参数不能赋给类型“string | number”的参数。ts(2345)
  ```
- 注意与联合数组做区别
  - ```js
    let person2: (string|number)[] = [1, 'ddd']
    person2[0] = 'ddd'
    person2[1] = 1
    person2[3] = 2
    // 不报错哦，可看出有区别于person1，这算是union array，联合数组
    ```

### 1.2 枚举
枚举是组织收集有关联变量的一种方式，许多程序语言（如：c/c#/Java）都有枚举数据类型。TypeScript也增加了一样的枚举类型

#### 数字枚举
- 一般使用就是直接与数字对应相关的
  ```js
  enum Color1 {
    red,
    green,
    blue
  }

  let color1 = Color1.blue

  // 打印的是blue在枚举中的顺序
  console.log('color1', color1) // 2
  ```
- 改变与数字枚举关联的数字
  ```js
  // 变成从5开始算
  enum Color2 {
    red = 5,
    green,
    blue
  }

  let color2 = Color2.blue

  console.log('color2', color2) // 7
  ```

#### 字符串枚举
- 使用字符串进行指定枚举
  ```js
  enum Color4 {
    red = 'red',
    green = 'green',
    blue = 'blue'
  }

  let color4 = Color4.green

  console.log('color4', color4) // green
  ```

#### 异构枚举
- 将数字与字符串混用
  ```js
  enum Color3 {
    red = 'red',
    green = 'green',
    blue = 3,
    yellow = 5
  }
  ```

#### 枚举成员

- 常量枚举成员,
  - 当一个表达式满足下面条件之一时，它就是一个常量枚举表达式
    -   一个枚举表达式字面量（主要是字符串字面量或数字字面量）
    -   一个对之前定义的常量枚举成员的引用（可以是在不同的枚举类型中定义的）
    -   带括号的常量枚举表达式
    -   一元运算符 `+`, `-`, `~`其中之一应用在了常量枚举表达式
    -   常量枚举表达式做为二元运算符 `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^`的操作对象。 若常数枚举表达式求值后为 `NaN`或 `Infinity`，则会在编译阶段报错。
  - ```js
    enum Color1 {
      red,
      green,
      blue
    }

    let color1 = Color1.blue
  
    // 打印的是blue在枚举中的顺序
    console.log('color1', color1) // 2
    ```

- 计算枚举，如下所示的yellow对应枚举值需要计算`plm`的长度才能得到
  - ```js
    enum Color5 {
      red,
      green,
      blue,
      yellow = 'plm'.length
    }

    let color5 = Color5.yellow

    console.log('color5', color5) // 3
    ```

#### 外部枚举
外部枚举（Ambient Enums）是使用 `declare enum` 定义的枚举类型，常用于定义已经存在的枚举对象，可以用于编译时对全文上下文的变量校验检查
- `declare` 定义的类型只会用于编译时的检查，是为了校验，编译结果中会被删除
  - ```js
    declare enum Directions {
        Up,
        Down,
        Left,
        Right
    }

    let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]
    ```
- 上例的编译结果是
    ```js
    var directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right];
    ```
- [# typescript 枚举](https://www.jianshu.com/p/3a040670aa65)