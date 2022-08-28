## 异步函数 async function

async关键字用于声明一个异步函数：

- async是asynchronous单词的缩写，异步、非同步；
- sync是synchronous单词的缩写，同步、同时；

async异步函数可以有很多中写法：

![image-20220725110015779](.\20_async-await事件循环\image-20220725110015779.png)





## 异步函数的执行流程

异步函数的内部代码执行过程和普通的函数是一致的，默认情况下也是会被同步执行。

![image-20220728060917793](.\20_async-await事件循环\image-20220728060917793.png)

异步函数有返回值时，和普通函数会有区别：

- 情况一：异步函数也可以有返回值，但是异步函数的返回值会被包裹到Promise.resolve中；

  ![image-20220728061121517](.\20_async-await事件循环\image-20220728061121517.png)

  ![image-20220728061436961](.\20_async-await事件循环\image-20220728061436961.png)

- 情况二：如果我们的异步函数的返回值是Promise，Promise.resolve的状态会由Promise决定；

  ![image-20220728061814936](.\20_async-await事件循环\image-20220728061814936.png)



- 情况三：如果我们的异步函数的返回值是一个对象并且实现了thenable，那么会由对象的then方法来决定；

  ![image-20220728061631268](.\20_async-await事件循环\image-20220728061631268.png)

如果我们在async中抛出了异常，那么程序它并不会像普通函数一样报错，而是会作为Promise的reject来传递；

同步代码中的异常，会让程序崩掉，后面的代码也不会再运行了，但是异步代码中的异常不会让程序崩掉，后面的代码会继续运行

![image-20220728062133152](.\20_async-await事件循环\image-20220728062133152.png)







## await关键字

async函数另外一个特殊之处就是可以在它内部使用await关键字，而普通函数中是不可以的。

await关键字有什么特点呢？

- 通常使用await是后面会跟上一个表达式，这个表达式会返回一个Promise；
- 那么await会等到Promise的状态变成fulfilled状态，之后继续执行异步函数；

![image-20220728062635759](.\20_async-await事件循环\image-20220728062635759.png)

![image-20220728062838327](.\20_async-await事件循环\image-20220728062838327.png)



如果await后面是一个普通的值，那么会直接返回这个值，相当于resolve了这个值；

如果await后面是一个thenable的对象，那么会根据对象的then方法调用来决定后续的值；

![image-20220728063154364](.\20_async-await事件循环\image-20220728063154364.png)

![image-20220728063258307](.\20_async-await事件循环\image-20220728063258307.png)



如果await后面的表达式，返回的Promise是reject的状态，那么会将这个reject结果直接作为函数的Promise的 reject值；

![image-20220728063516081](.\20_async-await事件循环\image-20220728063516081.png)





## 进程和线程

操作系统是硬件和软件之间链接的桥梁

线程和进程是操作系统中的两个概念：

- 进程（process）：计算机已经运行的程序，是操作系统管理程序的一种方式；
  - 一个应用程序就是一个进程，比如一个qq，一个微信，但是一个应用程序也不一定就只有一个进程
- 线程（thread）：操作系统能够运行运算调度的最小单位，通常情况下它被包含在进程中；
  - 微信也是有很多代码的，执行这些代码就是通过线程来执行的

听起来很抽象，这里还是给出我的解释：

- 进程：我们可以认为，启动一个应用程序，就会默认启动一个进程（也可能是多个进程）；
- 线程：每一个进程中，都会启动至少一个线程用来执行程序中的代码，这个线程被称之为主线程；
  - 一个进程可能有很多线程
- 所以我们也可以说进程是线程的容器；

再用一个形象的例子解释：

- 操作系统类似于一个大工厂；
- 工厂中里有很多车间，这个车间就是进程，进程本身不做事情；
- 每个车间可能有一个以上的工人在工厂，这个工人就是线程，事情都是工人在做的；







## 操作系统 – 进程 – 线程

![image-20220725110425367](.\20_async-await事件循环\image-20220725110425367.png)





## 操作系统的工作方式

操作系统是如何做到同时让多个进程（边听歌、边写代码、边查阅资料）同时工作呢？

- 如果cpu是单核的会在线程中快速切换，如果是多核的会同时运行多个线程

- 这是因为CPU的运算速度非常快，它可以快速的在多个进程之间迅速的切换；
- 当我们进程中的线程获取到时间片时，就可以快速执行我们编写的代码；
- 对于用户来说是感受不到这种快速的切换的；

你可以在Mac的活动监视器或者Windows的资源管理器中查看到很多进程：

![image-20220726081235097](.\20_async-await事件循环\image-20220726081235097.png)

这里有进程名称，还有线程，比如网易云音乐是一个进程，它开启了19个线程







## 浏览器中的JavaScript线程

我们经常会说JavaScript是单线程的，但是JavaScript的线程应该有自己的容器进程：浏览器或者Node。 

浏览器是一个进程吗，它里面只有一个线程吗？

- 目前多数的浏览器其实都是多进程的，当我们打开一个tab页面时就会开启一个新的进程，这是为了防止一个页 面卡死而造成所有页面无法响应，整个浏览器需要强制退出；
- 每个进程中又有很多的线程，其中包括执行JavaScript代码的线程；

![image-20220728065625611](.\20_async-await事件循环\image-20220728065625611.png)

JavaScript的代码执行是在一个单独的线程中执行的：

- 这就意味着JavaScript的代码，在同一个时刻只能做一件事；
- 如果这件事是非常耗时的，就意味着当前的线程就会被阻塞；

所以真正耗时的操作，实际上并不是由JavaScript线程在执行的：

- 浏览器的每个进程是多线程的，那么其他线程可以来完成这个耗时的操作；
- 比如网络请求、定时器，我们只需要在特性的时候执行应该有的回调即可；
  - 网络请求，只是你发了一个指令，发送指令以后，浏览器建立链接，发送数据，断开链接，等等（浏览器的其他线程做的）这些事情不是js做的，沟通完了以后，告诉js线程，也就是再回调，这个网络请求执行完了
  - 定时器，当我们开启定时器的时候，我们给浏览器发送一个指令，告诉浏览器我们要做一个2秒钟的定时，浏览器拿到这个指令以后会做一个两秒钟的定时，然后再回到js线程，回调2秒钟的函数
    - 这个两秒钟是由浏览器的其他线程做的
    - 这个过程中，浏览器怎么知道是否两秒钟到了，我们可以去执行回调2秒钟的函数呢？
    - 当时间到了以后，它是把他放到事件队列中的，js线程会去队列中取的

![image-20220728070953130](.\20_async-await事件循环\image-20220728070953130.png)



## 浏览器的事件循环

如果在执行JavaScript代码的过程中，有异步操作呢？





- 中间我们插入了一个setTimeout的函数调用；
- 这个函数被放到入调用栈中，执行会立即结束，并不会阻塞后续代码的执行；

![image-20220726081426047](.\20_async-await事件循环\image-20220726081426047.png)

setTimeout是同步的，之所以说setTimeout异步，是只setTimeout传入的函数是异步函数

![image-20220728071421246](.\20_async-await事件循环\image-20220728071421246.png)

在执行的过程中，setTimeout不会立即执行这个函数，而是在1秒钟之后执行，但是这个时候js线程不会等待一秒钟之后执行后面的代码，而是马上执行后面的代码，那么这个一秒钟之后执行的函数会被浏览器放到其他的地方（事件队列中），然后浏览器开始计时（计时操作也是一个线程，浏览器会执行相应的代码）。

那么一秒钟之后是怎么执行这个函数的呢？

浏览器维护着一个队列的，被称为事件队列，默认是空的

队列本身是一个数据结构，是一个线性结构，特点是先进先出

当我们执行到计时器，发现有一个函数是一秒钟之后执行，那么我们会把这个函数放到队列中

当我们的时间到了以后，js线程会从这个队列中取到这个函数并执行

![image-20220728072021261](.\20_async-await事件循环\image-20220728072021261.png)

![image-20220728072256385](.\20_async-await事件循环\image-20220728072256385.png)



js进程、其他进程、事件队列这三部分组成了一个环，这个环就被称为浏览器的事件循环



![image-20220726081441955](.\20_async-await事件循环\image-20220726081441955.png)





## 宏任务和微任务

但是事件循环中并非只维护着一个队列，事实上是有两个队列：

- 宏任务队列（macrotask queue）：ajax、setTimeout、setInterval、DOM监听、UI Rendering等
- 微任务队列（microtask queue）：Promise的then回调、 Mutation Observer API、queueMicrotask()等

![image-20220728073032227](.\20_async-await事件循环\image-20220728073032227.png)

这些都是回调，它们都不是立即执行的

在js线程中，立即执行的函数，都不是这种回调函数

![image-20220728073115334](.\20_async-await事件循环\image-20220728073115334.png)

这种代码才是立即执行的函数，它是在最顶层的，被称为main script



继续看上面的回调函数

![image-20220728073300041](.\20_async-await事件循环\image-20220728073300041.png)

queuemicrotask的回调和setTimeout中的回调是加入到同一个队列的吗？

并不是

我们加入的队列是不一样的

事实上有两个队列，一个称为宏任务队列，另一个称为微任务队列

![image-20220728073435678](.\20_async-await事件循环\image-20220728073435678.png)

不同的操作放到不同的队列

![image-20220728073544515](.\20_async-await事件循环\image-20220728073544515.png)

那么怎么执行这两个队列的事件呢？

是有一些规范的

![image-20220728073650588](.\20_async-await事件循环\image-20220728073650588.png)





那么事件循环对于两个队列的优先级是怎么样的呢？

- 1.main script中的代码优先执行（编写的顶层script代码）；
- 2.在执行任何一个宏任务之前（不是队列，是一个宏任务），都会先查看微任务队列中是否有任务需要执行
  - 也就是宏任务执行之前，必须保证微任务队列是空的；
  - 如果不为空，那么就优先执行微任务队列中的任务（回调）；



下面我们通过几到面试题来练习一下。





## Promise面试题

![image-20220726081630372](.\20_async-await事件循环\image-20220726081630372.png)

![image-20220726081640316](.\20_async-await事件循环\image-20220726081640316.png)





## promise async await 面试题

![image-20220726081659677](.\20_async-await事件循环\image-20220726081659677.png)

![image-20220726081708073](.\20_async-await事件循环\image-20220726081708073.png)





## Promise较难面试题

![image-20220726081809664](.\20_async-await事件循环\image-20220726081809664.png)

```js
Promise.resolve().then(() => {
  console.log(0);
  // 1.直接return一个值 相当于resolve(4)
  // return 4

  // 2.return thenable的值
  return {
    then: function(resolve) {
      // 大量的计算
      resolve(4)
    }
  }

  // 3.return Promise
  // 不是普通的值, 多加一次微任务
  // Promise.resolve(4), 多加一次微任务
  // 一共多加两次微任务
  return Promise.resolve(4)
}).then((res) => {
  console.log(res)
})

Promise.resolve().then(() => {
  console.log(1);
}).then(() => {
  console.log(2);
}).then(() => {
  console.log(3);
}).then(() => {
  console.log(5);
}).then(() =>{
  console.log(6);
})

// 1.return 4
// 0
// 1
// 4
// 2
// 3
// 5
// 6

// 2.return thenable
// 0
// 1
// 2
// 4
// 3
// 5
// 6

// 3.return promise
// 0
// 1
// 2
// 3
// 4
// 5
// 6
```







## Node的事件循环

当用node来执行代码的时候，它也会给你开启一个进程的

node进程也是多线程的

其中有一个js线程，js线程是用来执行js代码的

其中有线程可以执行setTimeout操作

也有线程执行文件读取操作

也有线程执行网络请求操作

这些操作都是由node中的其他线程来做的

其他线程做完这些操作之后，将做完这些事情之后，执行一个回调函数，将回调函数加入到队列中

然后js线程回调这些函数，获取到结果

所有node的事件循环和浏览器的事件循环是差不多的



浏览器中的EventLoop是根据HTML5定义的规范来实现的，不同的浏览器可能会有不同的实现，而Node中是由 libuv实现的。

这里我们来给出一个Node的架构图：

- 我们会发现libuv中主要维护了一个EventLoop和worker threads（线程池）；
- EventLoop负责调用系统的一些其他操作：文件的IO、Network、child-processes等

libuv是一个多平台的专注于异步IO的库，它最初是为Node开发的，但是现在也被使用到Luvit、Julia、pyuv等其 他地方；

![image-20220726082007283](.\20_async-await事件循环\image-20220726082007283.png)

![image-20220731092142266](.\20_async-await事件循环\image-20220731092142266.png)

node其实更多的做的是一个文件的操作，浏览器做的更多的是渲染的操作，它们是的大同小异的



## Node事件循环的阶段

我们最前面就强调过，事件循环像是一个桥梁，是连接着应用程序的JavaScript和系统调用之间的通道：

- 无论是我们的文件IO、数据库、网络IO、定时器、子进程，在完成对应的操作后，都会将对应的结果和回调函 数放到事件循环（任务队列）中；
- 事件循环会不断的从任务队列中取出对应的事件（回调函数）来执行；
- 所以js是不会发送网络请求的，网络请求是浏览器去做的，浏览器是调用操作系统去做的
- 所以说，时间循环更像是一个桥梁，它是链接我们js做不了的东西
- 所以为什么说早起js是用在浏览器的
- 但是现在js也可以用在浏览器开发中，为什么可以用在服务器中呢？服务器开发有什么要求呢？
- 就是进行IO的操作，I就是input，O就是output操作
- 也就是读取一些到程序里面
- 写入一些到程序外面
- 可以写入一个文件到本地里面
- 也可以输入一些文件到数据库中
- 但是现在可以通过libuv来调用相对的任务写入或者输出任务
- 这就是为什么js可以用在服务器开发，就是因为node通过libuv可以操作很多东西

但是一次完整的事件循环Tick分成很多个阶段：

tick可以想象成钟表中秒针的一次滴答

一次tick就成为一次滴答

这些就是一次tick做的事情

- 定时器（Timers）：本阶段执行已经被 setTimeout() 和 setInterval() 的调度回调函数。
- 待定回调（Pending Callback）：对某些系统操作（如TCP错误类型）执行回调，比如TCP连接时接收到 ECONNREFUSED（error connection refused)。
- idle, prepare：仅系统内部使用。
- 轮询（Poll）：检索新的 I/O 事件；执行与 I/O 相关的回调；
- 检测（check）：setImmediate() 回调函数在这里执行。
- 关闭的回调函数：一些关闭的回调函数，如：socket.on('close', ...)。

node实际上维护了非常多的队列，这些每一个可能都属于一个队列





## Node事件循环的阶段图解

![image-20220726082151226](.\20_async-await事件循环\image-20220726082151226.png)



上面这些都是宏任务



## Node的宏任务和微任务

我们会发现从一次事件循环的Tick来说，Node的事件循环更复杂，它也分为微任务和宏任务：

- 宏任务（macrotask）：setTimeout、setInterval、IO事件、setImmediate、close事件；
- 微任务（microtask）：Promise的then回调、process.nextTick、queueMicrotask；

但是，Node中的事件循环不只是 微任务队列和 宏任务队列：

- 微任务队列：
  - next tick queue：process.nextTick；
  - other queue：Promise的then回调、queueMicrotask；
- 宏任务队列：
  - timer queue：setTimeout、setInterval；
  - poll queue：IO事件；
  - check queue：setImmediate；
  - close queue：close事件；

node的宏任务和微任务也是和浏览器一样的，都是微任务执行完了以后才执行宏任务的





## Node事件循环的顺序

所以，在每一次事件循环的tick中，会按照如下顺序来执行代码：

- next tick microtask queue；
- other microtask queue；
- timer queue；
- poll queue；
- check queue；
- close queue；





## Node执行面试题

![image-20220726082442699](.\20_async-await事件循环\image-20220726082442699.png)

![image-20220726082454272](.\20_async-await事件循环\image-20220726082454272.png)





## 错误处理方案

开发中我们会封装一些工具函数，封装之后给别人使用：

- 在其他人使用的过程中，可能会传递一些参数；
- 对于函数来说，需要对这些参数进行验证，否则可能得到的是我们不想要的结果；

很多时候我们可能验证到不是希望得到的参数时，就会直接return：

- 但是return存在很大的弊端：调用者不知道是因为函数内部没有正常执行，还是执行结果就是一个undefined；
- 事实上，正确的做法应该是如果没有通过某些验证，那么应该让外界知道函数内部报错了；

如何可以让一个函数告知外界自己内部出现了错误呢？

- 通过throw关键字，抛出一个异常；

throw语句：

- throw语句用于抛出一个用户自定义的异常；
- 当遇到throw语句时，当前的函数执行会被停止（throw后面的语句不会执行）；

如果我们执行代码，就会报错，拿到错误信息的时候我们可以及时的去修正代码。



```js
/**
 * 如果我们有一个函数, 在调用这个函数时, 如果出现了错误, 那么我们应该是去修复这个错误.
 */

function sum(num1, num2) {
  // 当传入的参数的类型不正确时, 应该告知调用者一个错误
  if (typeof num1 !== "number" || typeof num2 !== "number") {
    // return undefined
    throw "parameters is error type~"
  }

  return num1 + num2
}

// 调用者(如果没有对错误进行处理, 那么程序会直接终止)
// console.log(sum({ name: "why" }, true))
console.log(sum(20, 30))

console.log("后续的代码会继续运行~")
```

![image-20220802070415096](.\20_async-await事件循环\image-20220802070415096.png)



## throw关键字

throw表达式就是在throw后面可以跟上一个表达式来表示具体的异常信息：

![image-20220726082651420](.\20_async-await事件循环\image-20220726082651420.png)

throw关键字可以跟上哪些类型呢？

- 基本数据类型：比如number、string、Boolean
- 对象类型：对象类型可以包含更多的信息

但是每次写这么长的对象又有点麻烦，所以我们可以创建一个类：

![image-20220726082738510](.\20_async-await事件循环\image-20220726082738510.png)





## Error类型

事实上，JavaScript已经给我们提供了一个Error类，我们可以直接创建这个类的对象：

![image-20220726082806837](.\20_async-await事件循环\image-20220726082806837.png)

Error包含三个属性：

- messsage：创建Error对象时传入的message；
- name：Error的名称，通常和类的名称一致；
- stack：整个Error的错误信息，包括函数的调用栈，当我们直接打印Error对象时，打印的就是stack；

Error有一些自己的子类：

- RangeError：下标值越界时使用的错误类型；
- SyntaxError：解析语法错误时使用的错误类型；
- TypeError：出现类型错误时，使用的错误类型；

```js
// class HYError {
//   constructor(errorCode, errorMessage) {
//     this.errorCode = errorCode
//     this.errorMessage = errorMessage
//   }
// }

function foo(type) {
  console.log("foo函数开始执行")

  if (type === 0) {
    // 1.抛出一个字符串类型(基本的数据类型)
    // throw "error"

    // 2.比较常见的是抛出一个对象类型
    // throw { errorCode: -1001, errorMessage: "type不能为0~" }

    // 3.创建类, 并且创建这个类对应的对象
    // throw new HYError(-1001, "type不能为0~")

    // 4.提供了一个Error
    // const err = new Error("type不能为0")
    // err.name = "why" // 错误名称
    // err.stack = "aaaa" // stack是函数的调用栈
    // name和stack可以修改，但是一般不修改它
    // throw new Error('type不能为0')

    // 5.Error的子类
    const err = new TypeError("当前type类型是错误的~")

    throw err

    // 强调: 如果函数中已经抛出了异常, 那么后续的代码都不会继续执行了
    console.log("foo函数后续的代码")
  }

  console.log("foo函数结束执行")
}

foo(0)

console.log("后续的代码继续执行~")


// function test() {
//   console.log("test")
// }

// function demo() {
//   test()
// }

// function bar() {
//   demo()
// }

// bar()

```

![image-20220802071841907](.\20_async-await事件循环\image-20220802071841907.png)



## 异常的处理

我们会发现在之前的代码中，一个函数抛出了异常，调用它的时候程序会被强制终止：

- 这是因为如果我们在调用一个函数时，这个函数抛出了异常，但是我们并没有对这个异常进行处理，那么这个异常会继续传 递到上一个函数调用中；
- 而如果到了最顶层（全局）的代码中依然没有对这个异常的处理代码，这个时候就会报错并且终止程序的运行；

我们先来看一下这段代码的异常传递过程：

- foo函数在被执行时会抛出异常，也就是我们的bar函数会拿到这个异常；
- 但是bar函数并没有对这个异常进行处理，那么这个异常就会被继续传递到调用bar函数的函数，也就是test函数；
- 但是test函数依然没有处理，就会继续传递到我们的全局代码逻辑中；
- 依然没有被处理，这个时候程序会终止执行，后续代码都不会再执行了；

![image-20220726083015316](.\20_async-await事件循环\image-20220726083015316.png)

![image-20220726083024413](.\20_async-await事件循环\image-20220726083024413.png)

```js
function foo(type) {
  if (type === 0) {
    throw new Error("foo error message~")
  }
}

// 1.第一种是不处理, bar函数会继续将收到的异常直接抛出去
function bar() {
  // try {
  foo(0)
  //   console.log("bar函数后续的继续运行")
  // } catch(err) {
  //   console.log("err:", err.message)
  //   // alert(err.message)
  // } finally {
  //   console.log("finally代码执行~, close操作")
  // }
}

function test() {
  try {
    bar()
  } catch (error) {
    console.log("error:", error)
  }
}

function demo() {
  test()
}


// 两种处理方法:
// 1.第一种是不处理, 那么异常会进一步的抛出, 直到最顶层的调用
// 如果在最顶层也没有对这个异常进行处理, 那么我们的程序就会终止执行, 并且报错
// foo()
```





## 异常的捕获

但是很多情况下当出现异常时，我们并不希望程序直接推出，而是希望可以正确的处理异常：

- 这个时候我们就可以使用try catch

![image-20220726083109321](.\20_async-await事件循环\image-20220726083109321.png)

![image-20220726083118930](.\20_async-await事件循环\image-20220726083118930.png)





在ES10（ES2019）中，catch后面绑定的error可以省略。

![image-20220802072753884](.\20_async-await事件循环\image-20220802072753884.png)

当然，如果有一些必须要执行的代码，我们可以使用finally来执行：

- finally表示最终一定会被执行的代码结构；
- 注意：如果try和finally中都有返回值，那么会使用finally当中的返回值；



因为错误，会一层一层的随着调用栈往上抛，所以，我们可以在调用栈的任何一层来捕获错误，通过try-catch,但是如果我们在任何一层都没有捕获的话，那么就会抛出错误