## 异步任务的处理

在ES6出来之后，有很多关于Promise的讲解、文章，也有很多经典的书籍讲解Promise

- 虽然等你学会Promise之后，会觉得Promise不过如此，但是在初次接触的时候都会觉得这个东西不好理解；

那么这里我从一个实际的例子来作为切入点：

- 我们调用一个函数，这个函数中发送网络请求（我们可以用定时器来模拟）；
- 如果发送网络请求成功了，那么告知调用者发送成功，并且将相关数据返回过去；
- 如果发送网络请求失败了，那么告知调用者发送失败，并且告知错误信息；

![image-20220623072754947](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623072754947.png)

![image-20220623072812040](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623072812040.png)

```js
/**
 * 这种回调的方式有很多的弊端:
 *  1> 如果是我们自己封装的requestData,那么我们在封装的时候必须要自己设计好callback名称, 并且使用好
 *  2> 如果我们使用的是别人封装的requestData或者一些第三方库, 那么我们必须去看别人的源码或者文档, 才知道它这个函数需要怎么去获取到结果
 */

// request.js
function requestData(url, successCallback, failtureCallback) {
  // 模拟网络请求
  setTimeout(() => {
    // 拿到请求的结果
    // url传入的是coderwhy, 请求成功
    if (url === "coderwhy") {
      // 成功
      let names = ["abc", "cba", "nba"]
      successCallback(names)
    } else { // 否则请求失败
      // 失败
      let errMessage = "请求失败, url错误"
      failtureCallback(errMessage)
    }
  }, 3000);
}

// main.js
requestData("kobe", (res) => {
  console.log(res)
}, (err) => {
  console.log(err)
})

// 更规范/更好的方案 Promise承诺(规范好了所有的代码编写逻辑)
function requestData2() {
  return "承诺"
}

const chengnuo = requestData2()

```





## 什么是Promise呢？

在上面的解决方案中，我们确确实实可以解决请求函数得到结果之后，获取到对应的回调，但是它存在两个主要的 问题：

- 第一，我们需要自己来设计回调函数、回调函数的名称、回调函数的使用等；
- 第二，对于不同的人、不同的框架设计出来的方案是不同的，那么我们必须耐心去看别人的源码或者文档，以 便可以理解它这个函数到底怎么用；

我们来看一下Promise的API是怎么样的：

- Promise是ES6新增的

- Promise是一个类（构造函数），可以翻译成 承诺、许诺 、期约；
- 当我们需要给予调用者一个承诺：待会儿我会给你回调数据时，就可以创建一个Promise的对象；
- 在通过new创建Promise对象时，我们需要传入一个回调函数，我们称之为executor（执行）
  - 这个回调函数会被立即执行，并且给传入另外两个回调函数resolve、reject；
  - 当我们调用resolve回调函数时，会执行Promise对象的then方法传入的回调函数；
  - 当我们调用reject回调函数时，会执行Promise对象的catch方法传入的回调函数；





## Promise的代码结构

我们来看一下Promise代码结构：

![image-20220704071155265](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704071155265.png)

![image-20220623073013167](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073013167.png)

new Promise接收的是一个回调函数，并且这个回调函数会立即执行

![image-20220704071414814](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704071414814.png)

![image-20220704071454801](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704071454801.png)

内部也是会执行这个函数

上面的传入的这个函数被称为executor,执行者

![image-20220704071729851](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704071729851.png)

这个函数有两个参数

别人拿到Promise， 在成功的时候调resolve，在失败的时候调reject

![image-20220704072109438](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704072109438.png)

![image-20220704072221634](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704072221634.png)



上面Promise使用过程，我们可以将它划分成三个状态：

待定（pending）: 初始状态，既没有被兑现，也没有被拒绝；

- 当执行executor中的代码时，处于该状态；

已兑现（fulfilled）: 意味着操作成功完成；

- 执行了resolve时，处于该状态；

已拒绝（rejected）: 意味着操作失败;

- 执行了reject时，处于该状态；

Promise是一种机制，所有的调用方法，调用模式都已经给你规范好了，这样大家就都统一了，不会出现每个人在设计回调或者其他方式的时候没有办法达到统一，这个时候就要花费时间来沟通或者看文档，所以promise能解决这种问题





## Promise重构请求

那么有了Promise，我们就可以将之前的代码进行重构了：

![image-20220623073142098](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073142098.png)

```js
// request.js
function requestData(url,) {
  // 异步请求的代码会被放入到executor中
   //给别人返回一个承诺 
  return new Promise((resolve, reject) => {
    // 模拟网络请求
    setTimeout(() => {
      // 拿到请求的结果
      // url传入的是coderwhy, 请求成功
      if (url === "coderwhy") {
        // 成功
        let names = ["abc", "cba", "nba"]
        resolve(names)
      } else { // 否则请求失败
        // 失败
        let errMessage = "请求失败, url错误"
        reject(errMessage)
      }
    }, 3000);
  })
}

// main.js， node需要这样写
// then有两个回调函数，第一个是成功的回调，第二个是失败的回调
const promise = requestData("coderwhy")
promise.then((res) => {
  console.log("请求成功:", res)
}, (err) => {
   console.log("请求失败:", err)
})
// 或者这样写
Promise.then(res => {
    console.log('res', res)
}).catch((err) => {
    console.log(err)
})

// 在node不能这样写
Promise.then(res => {
    console.log('请求成功,=', res)
})

Promise.cath(() => {
    console.log('请求失败')
})

```



![image-20220704073126696](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704073126696.png)

成功的时候调resolve，

失败的时候调reject



![image-20220704073748503](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704073748503.png)

拥有promise之后，我就可以知道了，我不需要知道你里面的回调函数怎么写的，只需要知道你返回一个promise，我拿到你这个promise之后，我调用就行了，成功用then的第一个回调函数，失败我用第二个回调函数



上面的代码也可以像这样写

![image-20220704074124121](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704074124121.png)





## Executor

Executor是在创建Promise时需要传入的一个回调函数，这个回调函数会被立即执行，并且传入两个参数：

![image-20220623073215227](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073215227.png)

通常我们会在Executor中确定我们的Promise状态：

- 通过resolve，可以兑现（fulfilled）Promise的状态，我们也可以称之为已决议（resolved）；
- 通过reject，可以拒绝（reject）Promise的状态；

这里需要注意：一旦状态被确定下来，Promise的状态会被 锁死，该Promise的状态是不可更改的

- 在我们调用resolve的时候，如果resolve传入的值本身不是一个Promise，那么会将该Promise的状态变成 兑 现（fulfilled）；
- 在之后我们去调用reject时，已经不会有任何的响应了（并不是这行代码不会执行，而是无法改变Promise状 态）；

![image-20220704074405256](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704074405256.png)

![image-20220704074613911](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704074613911.png)

状态一旦确定下来，就是不可更改的

![image-20220704074808290](D:\studyMaterial\JS高级\笔记\18-promise\image-20220704074808290.png)

## resolve不同值的区别

情况一：如果resolve传入一个普通的值或者对象，那么这个值会作为then回调的参数；

情况二：如果resolve中传入的是另外一个Promise，那么这个新Promise会决定原Promise的状态：

情况三：如果resolve中传入的是一个对象，并且这个对象有实现then方法，那么会执行该then方法，并且根据 then方法的结果来决定Promise的状态：

![image-20220623073341626](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073341626.png)

![image-20220623073354679](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073354679.png)

![image-20220623073411871](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073411871.png)

![image-20220705063806163](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705063806163.png)

![image-20220705064021556](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705064021556.png)

![image-20220705064336832](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705064336832.png)

```js
/**
 * resolve(参数)
 *  1> 普通的值或者对象  pending -> fulfilled
 *  2> 传入一个Promise
 *    那么当前的Promise的状态会由传入的Promise来决定
 *    相当于状态进行了移交
 *  3> 传入一个对象, 并且这个对象有实现then方法(并且这个对象是实现了thenable接口)
 *    那么也会执行该then方法, 并且又该then方法决定后续状态
 */

// 1.传入Promise的特殊情况
// const newPromise = new Promise((resolve, reject) => {
//   // resolve("aaaaaa")
//   reject("err message")
// })

// new Promise((resolve, reject) => {
//   // pending -> fulfilled
//   resolve(newPromise)
// }).then(res => {
//   console.log("res:", res)
// }, err => {
//   console.log("err:", err)
// })

// 2.传入一个对象, 这个兑现有then方法
new Promise((resolve, reject) => {
  // pending -> fulfilled
  const obj = {
    then: function(resolve, reject) {
      // resolve("resolve message")
      reject("reject message")
    }
  }
  resolve(obj)
}).then(res => {
  console.log("res:", res)
}, err => {
  console.log("err:", err)
})

// eatable/runable, 也就是在这个对象里面实现了这么一个接口，一些其他语言的术语
const obj = {
  eat: function() {

  },
  run: function() {

  }
}

```





## then方法 – 接受两个参数

then方法是Promise对象上的一个方法：它其实是放在Promise的原型上的 Promise.prototype.then

![image-20220705065626019](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705065626019.png)

![image-20220705065734343](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705065734343.png)

这些都是对象方法

then方法接受两个参数：

- fulfilled的回调函数：当状态变成fulfilled时会回调的函数；
- reject的回调函数：当状态变成reject时会回调的函数；

![image-20220623073500507](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073500507.png)





## then方法 – 多次调用

一个Promise的then方法是可以被多次调用的：

- 每次调用我们都可以传入对应的fulfilled回调；
- 当Promise的状态变成fulfilled的时候，这些回调函数都会被执行；

![image-20220623073549979](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073549979.png)

![image-20220705070127871](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705070127871.png)



## then方法 – 返回值

then方法本身是有返回值的，它的返回值是一个Promise，所以我们可以进行如下的链式调用：

- 但是then方法返回的Promise到底处于什么样的状态呢？

Promise有三种状态，那么这个Promise处于什么状态呢？

- 当then方法中的回调函数本身在执行的时候，那么它处于pending状态；
- 当then方法中的回调函数返回一个结果时，那么它处于fulfilled状态，并且会将结果作为resolve的参数；
  - 情况一：返回一个普通的值；
  - 情况二：返回一个Promise；
  - 情况三：返回一个thenable值；
- 当then方法抛出一个异常时，那么它处于reject状态；

![image-20220705070516852](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705070516852.png)

这是本质

如果没有返回呢？那么默认返回的其实是undefined

![image-20220705070638472](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705070638472.png)

![image-20220705070915983](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705070915983.png)

![image-20220705071055837](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705071055837.png)

![image-20220705071259945](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705071259945.png)

![image-20220705071449288](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705071449288.png)

当executor抛出异常时，也是会调用错误捕获的回调函数的

上面这种阅读性比较差，所以有其他的一种写法



## catch方法 – 多次调用

catch方法也是Promise对象上的一个方法：它也是放在Promise的原型上的 Promise.prototype.catch

一个Promise的catch方法是可以被多次调用的：

- 每次调用我们都可以传入对应的reject回调；
- 当Promise的状态变成reject的时候，这些回调函数都会被执行；

 ![image-20220623073757512](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073757512.png)

![image-20220705071654870](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705071654870.png)

但是这种写法，不符合promise/a+的规范

![image-20220705072140289](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705072140289.png)

![image-20220705072545496](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705072545496.png)

所以在node中必须在then里面有异常处理函数

或者有catch处理函数

![image-20220705072650496](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705072650496.png)

![image-20220705072827658](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705072827658.png)



## catch方法 – 返回值

事实上catch方法也是会返回一个Promise对象的，所以catch方法后面我们可以继续调用then方法或者catch方法：

- 下面的代码，后续是catch中的err2打印，还是then中的res打印呢？
- 答案是res打印，这是因为catch传入的回调在执行完后，默认状态依然会是fulfilled的；

![image-20220623073840128](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073840128.png)

那么如果我们希望后续继续执行catch，那么需要抛出一个异常：

![image-20220623073859237](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073859237.png)

我们知道then里面的返回值会包裹一个promise，那么catch里面呢？它的返回值会怎么样呢？

![image-20220705073304078](D:\studyMaterial\JS高级\笔记\18-promise\image-20220705073304078.png)





## finally方法

finally是在ES9（ES2018）中新增的一个特性：表示无论Promise对象无论变成fulfilled还是reject状态，最终都会 被执行的代码。

finally方法是不接收参数的，因为无论前面是fulfilled状态，还是reject状态，它都会执行。

![image-20220623073940743](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623073940743.png)





## resolve方法

前面我们学习的then、catch、finally方法都属于Promise的实例方法，都是存放在Promise的prototype上的。

- 下面我们再来学习一下Promise的类方法。

有时候我们已经有一个现成的内容了，希望将其转成Promise来使用，这个时候我们可以使用 Promise.resolve 方 法来完成。

- Promise.resolve的用法相当于new Promise，并且执行resolve操作：

![image-20220623074033654](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623074033654.png)



resolve参数的形态：

- 情况一：参数是一个普通的值或者对象
- 情况二：参数本身是Promise
- 情况三：参数是一个thenable





## reject方法

reject方法类似于resolve方法，只是会将Promise对象的状态设置为reject状态。

Promise.reject的用法相当于new Promise，只是会调用reject：

![image-20220623074131987](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623074131987.png)

Promise.reject传入的参数无论是什么形态，都会直接作为reject状态的参数传递到catch的。







## all方法

另外一个类方法是Promise.all：

- 它的作用是将多个Promise包裹在一起形成一个新的Promise；
- 新的Promise状态由包裹的所有Promise共同决定：
  - 当所有的Promise状态变成fulfilled状态时，新的Promise状态为fulfilled，并且会将所有Promise的返回值 组成一个数组；
  - 当有一个Promise状态为reject时，新的Promise状态为reject，并且会将第一个reject的返回值作为参数；

![image-20220623074232866](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623074232866.png)

![image-20220623074249435](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623074249435.png)







## allSettled方法

all方法有一个缺陷：当有其中一个Promise变成reject状态时，新Promise就会立即变成对应的reject状态。

- 那么对于resolved的，以及依然处于pending状态的Promise，我们是获取不到对应的结果的；

在ES11（ES2020）中，添加了新的API Promise.allSettled：

- 该方法会在所有的Promise都有结果（settled），无论是fulfilled，还是reject时，才会有最终的状态；
- 并且这个Promise的结果一定是fulfilled的；

![image-20220623074345939](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623074345939.png)

我们来看一下打印的结果：

- allSettled的结果是一个数组，数组中存放着每一个Promise的结果，并且是对应一个对象的；
- 这个对象中包含status状态，以及对应的value值；





## race方法

如果有一个Promise有了结果，我们就希望决定最终新Promise的状态，那么可以使用race方法：

- race是竞技、竞赛的意思，表示多个Promise相互竞争，谁先有结果，那么就使用谁的结果；

![image-20220623074438966](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623074438966.png)





## any方法

any方法是ES12中新增的方法，和race方法是类似的：

- any方法会等到一个fulfilled状态，才会决定新Promise的状态；
- 如果所有的Promise都是reject的，那么也会等到所有的Promise都变成rejected状态；

![image-20220623074527934](D:\studyMaterial\JS高级\笔记\Untitled\image-20220623074527934.png)

如果所有的Promise都是reject的，那么会报一个AggregateError的错误。