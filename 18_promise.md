## 异步任务的处理

在ES6出来之后，有很多关于Promise的讲解、文章，也有很多经典的书籍讲解Promise

- 虽然等你学会Promise之后，会觉得Promise不过如此，但是在初次接触的时候都会觉得这个东西不好理解；

那么这里我从一个实际的例子来作为切入点：

- 我们调用一个函数，这个函数中发送网络请求（我们可以用定时器来模拟）；
- 如果发送网络请求成功了，那么告知调用者发送成功，并且将相关数据返回过去；
- 如果发送网络请求失败了，那么告知调用者发送失败，并且告知错误信息；

![image-20220623072754947](.\18_promise\image-20220623072754947.png)

![image-20220623072812040](.\18_promise\image-20220623072812040.png)

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

![image-20220704071155265](.\18_promise\image-20220704071155265.png)

![image-20220623073013167](.\18_promise\image-20220623073013167.png)

new Promise接收的是一个回调函数，并且这个回调函数会立即执行

![image-20220704071414814](.\18_promise\image-20220704071414814.png)

![image-20220704071454801](.\18_promise\image-20220704071454801.png)

内部也是会执行这个函数

上面的传入的这个函数被称为executor,执行者

![image-20220704071729851](.\18_promise\image-20220704071729851.png)

这个函数有两个参数

别人拿到Promise， 在成功的时候调resolve，在失败的时候调reject

![image-20220704072109438](.\18_promise\image-20220704072109438.png)

![image-20220704072221634](.\18_promise\image-20220704072221634.png)



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

![image-20220623073142098](.\18_promise\image-20220623073142098.png)

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



![image-20220704073126696](.\18_promise\image-20220704073126696.png)

成功的时候调resolve，

失败的时候调reject



![image-20220704073748503](.\18_promise\image-20220704073748503.png)

拥有promise之后，我就可以知道了，我不需要知道你里面的回调函数怎么写的，只需要知道你返回一个promise，我拿到你这个promise之后，我调用就行了，成功用then的第一个回调函数，失败我用第二个回调函数



上面的代码也可以像这样写

![image-20220704074124121](.\18_promise\image-20220704074124121.png)





## Executor

Executor是在创建Promise时需要传入的一个回调函数，这个回调函数会被立即执行，并且传入两个参数：

![image-20220623073215227](.\18_promise\image-20220623073215227.png)

通常我们会在Executor中确定我们的Promise状态：

- 通过resolve，可以兑现（fulfilled）Promise的状态，我们也可以称之为已决议（resolved）；
- 通过reject，可以拒绝（reject）Promise的状态；

这里需要注意：一旦状态被确定下来，Promise的状态会被 锁死，该Promise的状态是不可更改的

- 在我们调用resolve的时候，如果resolve传入的值本身不是一个Promise，那么会将该Promise的状态变成 兑 现（fulfilled）；
- 在之后我们去调用reject时，已经不会有任何的响应了（并不是这行代码不会执行，而是无法改变Promise状 态）；

![image-20220704074405256](.\18_promise\image-20220704074405256.png)

![image-20220704074613911](.\18_promise\image-20220704074613911.png)

状态一旦确定下来，就是不可更改的

![image-20220704074808290](.\18_promise\image-20220704074808290.png)

## resolve不同值的区别

情况一：如果resolve传入一个普通的值或者对象，那么这个值会作为then回调的参数；

情况二：如果resolve中传入的是另外一个Promise，那么这个新Promise会决定原Promise的状态：

情况三：如果resolve中传入的是一个对象，并且这个对象有实现then方法，那么会执行该then方法，并且根据 then方法的结果来决定Promise的状态：

![image-20220623073341626](.\18_promise\image-20220623073341626.png)

![image-20220623073354679](.\18_promise\image-20220623073354679.png)

![image-20220623073411871](.\18_promise\image-20220623073411871.png)

![image-20220705063806163](.\18_promise\image-20220705063806163.png)

![image-20220705064021556](.\18_promise\image-20220705064021556.png)

![image-20220705064336832](.\18_promise\image-20220705064336832.png)

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

![image-20220705065626019](.\18_promise\image-20220705065626019.png)

![image-20220705065734343](.\18_promise\image-20220705065734343.png)

这些都是对象方法

then方法接受两个参数：

- fulfilled的回调函数：当状态变成fulfilled时会回调的函数；
- reject的回调函数：当状态变成reject时会回调的函数；

![image-20220623073500507](.\18_promise\image-20220623073500507.png)





## then方法 – 多次调用

一个Promise的then方法是可以被多次调用的：

- 每次调用我们都可以传入对应的fulfilled回调；
- 当Promise的状态变成fulfilled的时候，这些回调函数都会被执行；

![image-20220623073549979](.\18_promise\image-20220623073549979.png)

![image-20220705070127871](.\18_promise\image-20220705070127871.png)



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

![image-20220705070516852](.\18_promise\image-20220705070516852.png)

这是本质

如果没有返回呢？那么默认返回的其实是undefined

![image-20220705070638472](.\18_promise\image-20220705070638472.png)

![image-20220705070915983](.\18_promise\image-20220705070915983.png)

![image-20220705071055837](.\18_promise\image-20220705071055837.png)

![image-20220705071259945](.\18_promise\image-20220705071259945.png)

![image-20220705071449288](.\18_promise\image-20220705071449288.png)

当executor抛出异常时，也是会调用错误捕获的回调函数的

上面这种阅读性比较差，所以有其他的一种写法



## catch方法 – 多次调用

catch方法也是Promise对象上的一个方法：它也是放在Promise的原型上的 Promise.prototype.catch

一个Promise的catch方法是可以被多次调用的：

- 每次调用我们都可以传入对应的reject回调；
- 当Promise的状态变成reject的时候，这些回调函数都会被执行；

 ![image-20220623073757512](.\18_promise\image-20220623073757512.png)

![image-20220705071654870](.\18_promise\image-20220705071654870.png)

但是这种写法，不符合promise/a+的规范

![image-20220705072140289](.\18_promise\image-20220705072140289.png)

![image-20220705072545496](.\18_promise\image-20220705072545496.png)

所以在node中必须在then里面有异常处理函数

或者有catch处理函数

![image-20220705072650496](.\18_promise\image-20220705072650496.png)

![image-20220705072827658](.\18_promise\image-20220705072827658.png)



## catch方法 – 返回值

事实上catch方法也是会返回一个Promise对象的，所以catch方法后面我们可以继续调用then方法或者catch方法：

- 下面的代码，后续是catch中的err2打印，还是then中的res打印呢？
- 答案是res打印，这是因为catch传入的回调在执行完后，默认状态依然会是fulfilled的；

![image-20220623073840128](.\18_promise\image-20220623073840128.png)

那么如果我们希望后续继续执行catch，那么需要抛出一个异常：

![image-20220623073859237](.\18_promise\image-20220623073859237.png)

我们知道then里面的返回值会包裹一个promise，那么catch里面呢？它的返回值会怎么样呢？

![image-20220705073304078](.\18_promise\image-20220705073304078.png)





## finally方法

finally是在ES9（ES2018）中新增的一个特性：表示无论Promise对象无论变成fulfilled还是reject状态，最终都会 被执行的代码。

finally方法是不接收参数的，因为无论前面是fulfilled状态，还是reject状态，它都会执行。

![image-20220623073940743](.\18_promise\image-20220623073940743.png)





# 类方法

## resolve方法

前面我们学习的then、catch、finally方法都属于Promise的实例方法，都是存放在Promise的prototype上的。

- 下面我们再来学习一下Promise的类方法。

有时候我们已经有一个现成的内容了，希望将其转成Promise来使用，这个时候我们可以使用 Promise.resolve 方 法来完成。

- Promise.resolve的用法相当于new Promise，并且执行resolve操作：

![image-20220623074033654](.\18_promise\image-20220623074033654.png)



resolve参数的形态：

- 情况一：参数是一个普通的值或者对象
- 情况二：参数本身是Promise
- 情况三：参数是一个thenable

```js
// 如果想让obj对象变成一个promise可以通过返回一个promise来获取
function foo() {
  const obj = {name: 'wts'};
  return new Promise((resolve) => {
    resolve(obj);
  })
}
foo.then(res => {
    // 这样就可以通过.then的方式来获取obj
    console.log(res)
})
```



```js
const promise = Promise.resolve({name: 'why'});	// 普通的值/promise/thenable对象
// 相当于
const promise2 = new Promise(resolve => {
    resolve({name: 'why'})
})

// 一样通过then来获取
promise.then(res => {
    console.log(res);
})
```









## reject方法

reject方法类似于resolve方法，只是会将Promise对象的状态设置为reject状态。

Promise.reject的用法相当于new Promise，只是会调用reject：

![image-20220623074131987](.\18_promise\image-20220623074131987.png)

Promise.reject传入的参数无论是什么形态，都会直接作为reject状态的参数传递到catch的。

```js
const promise = Promise.reject('reject message');	// 普通的值/promise/thenable对象
// 相当于
const promise2 = new Promise((resolve, reject) => {
    reject({name: 'why'})
})

// 一样通过then来获取
promise.then(res => {
    console.log(res);
}, (err) => {
    console.log(err)
})
// 或者
promise.then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
})

// 注意无论传入什么值，都是一样的
// 也就是说，就算这样写，依然只会调用.catch
const promise = Promise.reject({
    then: function (resolve, reject){
        resolve('11111')
    }
});

```

![image-20220706065832578](.\18_promise\image-20220706065832578.png)





## all方法

另外一个类方法是Promise.all：

- 它的作用是将多个Promise包裹在一起形成一个新的Promise；
- 新的Promise状态由包裹的所有Promise共同决定：
  - 当所有的Promise状态变成fulfilled状态时，新的Promise状态为fulfilled，并且会将所有Promise的返回值 组成一个数组；
  - 当有一个Promise状态为reject时，新的Promise状态为reject，并且会将第一个reject的返回值作为参数；

![image-20220623074232866](.\18_promise\image-20220623074232866.png)

![image-20220623074249435](.\18_promise\image-20220623074249435.png)

```js
const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(111)
    }, 1000)
})
const p2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(222)
    }, 2000)
})
const p3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(333)
    }, 3000)
})

// 需求： 所有的promise都变成fulfilled时，再拿到结果
// aaa如果加在里面，它也会变成promise
Promise.all([p1, p2, p3, 'aaa']).then(res => {
    console.log(res)
})
```

![image-20220706070417954](.\18_promise\image-20220706070417954.png)

它是按照输入数组的顺序返回的

那如果有意外怎么办呢？

```js
const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(111)
    }, 1000)
})
const p2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        reject(222)
    }, 2000)
})
const p3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(333)
    }, 3000)
})

// 需求： 所有的promise都变成fulfilled时，再拿到结果
// aaa如果加在里面，它也会变成promise
// 如果有一个promise的状态是rejected, 那么整个promise.all就会变成rejected
Promise.all([p1, p2, p3, 'aaa']).then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
})
```

![image-20220706070635392](.\18_promise\image-20220706070635392.png)



## allSettled方法

all方法有一个缺陷：当有其中一个Promise变成reject状态时，新Promise就会立即变成对应的reject状态。

- 那么对于resolved的，以及依然处于pending状态的Promise，我们是获取不到对应的结果的；

在ES11（ES2020）中，添加了新的API Promise.allSettled：

- 该方法会在所有的Promise都有结果（settled），无论是fulfilled，还是reject时，才会有最终的状态；
- 并且这个Promise的结果一定是fulfilled的；
- 不管有没有rejected，都不会进入catch

![image-20220623074345939](.\18_promise\image-20220623074345939.png)

我们来看一下打印的结果：

- allSettled的结果是一个数组，数组中存放着每一个Promise的结果，并且是对应一个对象的；
- 这个对象中包含status状态，以及对应的value值；

```js
const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(111)
    }, 1000)
})
const p2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        reject(222)
    }, 2000)
})
const p3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(333)
    }, 3000)
})

// 需求： 所有的promise都变成fulfilled时，再拿到结果
// aaa如果加在里面，它也会变成promise
// 如果有一个promise的状态是rejected, 那么整个promise.all就会变成rejected
Promise.allSettled([p1, p2, p3]).then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
})
```

![image-20220706071041971](.\18_promise\image-20220706071041971.png)

会在结果里面标志每一个promise的状态，比如有些promise的状态就是rejected



## race方法

race: 竞技，竞赛的意思

如果有一个Promise有了结果，我们就希望决定最终新Promise的状态，那么可以使用race方法：

- race是竞技、竞赛的意思，表示多个Promise相互竞争，谁先有结果，那么就使用谁的结果；
- 也就是只要有一个有结果了，race方法就判断出最终的结果了

![image-20220623074438966](.\18_promise\image-20220623074438966.png)

```js
const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(111)
    }, 1000)
})
const p2 = new Promise((resolve, reject) => {
    setTimeout(() => {
        reject(222)
    }, 2000)
})
const p3 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(333)
    }, 3000)
})

Promise.race([p1, p2, p3]).then(res => {
    console.log(res)
}).catch(err => {
    console.log(err)
})
```

![image-20220706071408274](.\18_promise\image-20220706071408274.png)

如果有promise先拒绝了，那么整个的结果就拒绝了

![image-20220706071534307](.\18_promise\image-20220706071534307.png)



那如果就想要等到一个结果，不管你reject，我就要等一个resolv, 怎么办呢



## any方法

any方法是ES12中新增的方法，和race方法是类似的：

- any方法会等到一个fulfilled状态，才会决定新Promise的状态；
- 如果所有的Promise都是reject的，那么也会等到所有的Promise都变成rejected状态；

![image-20220623074527934](.\18_promise\image-20220623074527934.png)

如果所有的Promise都是reject的，那么会报一个AggregateError的错误。

![image-20220706071843663](.\18_promise\image-20220706071843663.png)

![image-20220706071930607](.\18_promise\image-20220706071930607.png)

可以通过err.errors来拿到错误信息





## promise实现



### promise结构的设计

如果自己设计promise需要参考一个规范

promise是在ES6才出现的，也就是之前是没有promise的

但是在之前社区里面是有实现promise的

但是每个人实现的promise是不一样的

这样就造成很混乱

然后，社区就流行了一个规范，叫promisea+

https://promisesaplus.com/

他就写了，如果你想实现promise，应该有什么方法



```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  // 它会接收一个参数，这个参数就是一个函数
  constructor(executor) {
    // 默认状态是pending状态
    this.status = PROMISE_STATUS_PENDING;

    // 因为传进来的这个函数是有两个函数，一个是resolve, 一个是reject
    // 所以我们需要在这里实现这两个函数，并且给到executor中
      const resolve = () => {
        // 只有状态是pending的时候才能调用这个东西
        if(this.status === PROMISE_STATUS_PENDING){
          this.status = PROMISE_STATUS_FULFILLED
          console.log('resolve被调用')
        }
      }
      const reject = () => {
        if(this.status === PROMISE_STATUS_PENDING) {
          this.status = PROMISE_STATUS_REJECTED
          console.log('reject被调用')
        }
      }

      // 我们需要调用这个函数
      executor(resolve, reject);
  }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  resolve();
  reject();
})
```

状态简单管理实现了，也就是状态一旦变化，就不能改变了



保存参数

```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  // 它会接收一个参数，这个参数就是一个函数
  constructor(executor) {
    // 默认状态是pending状态
    this.status = PROMISE_STATUS_PENDING;
    // 保存传进来的参数
    this.value = undefined;
    this.reason = undefined;

    // 因为传进来的这个函数是有两个函数，一个是resolve, 一个是reject
    // 所以我们需要在这里实现这两个函数，并且给到executor中
    // 接收参数，这里叫value
      const resolve = (value) => {
        // 只有状态是pending的时候才能调用这个东西
        if(this.status === PROMISE_STATUS_PENDING){
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value;
          console.log('resolve被调用')
        }
      }
      // 这里的叫reason
      const reject = (reason) => {
        if(this.status === PROMISE_STATUS_PENDING) {
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason;
          console.log('reject被调用')
        }
      }

      // 我们需要调用这个函数
      executor(resolve, reject);
  }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  // 传进去的参数必须要保存下来
  resolve(111);
  reject(222);
})

promise.then(res => {
  console.log(res)
}, err => {
  console.log(err);
})


```



拿到then方法中的两个函数，并调用

```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  // 它会接收一个参数，这个参数就是一个函数
  constructor(executor) {
    // 默认状态是pending状态
    this.status = PROMISE_STATUS_PENDING;
    // 保存传进来的参数
    this.value = undefined;
    this.reason = undefined;

    // 因为传进来的这个函数是有两个函数，一个是resolve, 一个是reject
    // 所以我们需要在这里实现这两个函数，并且给到executor中
    // 接收参数，这里叫value
      const resolve = (value) => {
        // 只有状态是pending的时候才能调用这个东西
        if(this.status === PROMISE_STATUS_PENDING){
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value;
          this.onFulfilled(this.value);
          console.log('resolve被调用')
          // 这里要执行then传进来的回调
        }
      }
      // 这里的叫reason
      const reject = (reason) => {
        if(this.status === PROMISE_STATUS_PENDING) {
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason;
          console.log('reject被调用');
          this.onRejected(this.reason);
          // 这里要执行then传进来的第二个回调
        }
      }

      // 我们需要调用这个函数
      executor(resolve, reject);
  }
  // 调用.then的时候执行这个函数，并且接收两个函数
  then(onFulfilled, onRejected) {
    // 保存以后去调用
    this.onFulfilled = onFulfilled;
    this.onRejected = onRejected;
  }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  // 传进去的参数必须要保存下来
  resolve(111);
  reject(222);
})

promise.then(res => {
  console.log(res)
}, err => {
  console.log(err);
})


```



![image-20220707064518212](.\18_promise\image-20220707064518212.png)

会报错，因为先调用的是new 也就是会先执行constructor中的代码，但是，这个时候this.onFulfilled是没有值的

那么怎么执行这一点呢？

加个定时器吧

加个定时器，其实也是加一个宏任务，加了宏任务，实际上就是等到下一个事件循环的时候再执行，不会阻塞主线程的执行的

```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  // 它会接收一个参数，这个参数就是一个函数
  constructor(executor) {
    // 默认状态是pending状态
    this.status = PROMISE_STATUS_PENDING;
    // 保存传进来的参数
    this.value = undefined;
    this.reason = undefined;

    // 因为传进来的这个函数是有两个函数，一个是resolve, 一个是reject
    // 所以我们需要在这里实现这两个函数，并且给到executor中
    // 接收参数，这里叫value
      const resolve = (value) => {
        // 只有状态是pending的时候才能调用这个东西
        if(this.status === PROMISE_STATUS_PENDING){
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value;
          setTimeout(() => {
            this.onFulfilled(this.value);
          }, 0)
          console.log('resolve被调用')
          // 这里要执行then传进来的回调
        }
      }
      // 这里的叫reason
      const reject = (reason) => {
        if(this.status === PROMISE_STATUS_PENDING) {
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason;
          console.log('reject被调用');
          setTimeout(() => {
            this.onRejected(this.reason);
          }, 0)
          // 这里要执行then传进来的第二个回调
        }
      }

      // 我们需要调用这个函数
      executor(resolve, reject);
  }
  // 调用.then的时候执行这个函数，并且接收两个函数
  then(onFulfilled, onRejected) {
    // 保存以后去调用
    this.onFulfilled = onFulfilled;
    this.onRejected = onRejected;
  }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  // 传进去的参数必须要保存下来
  resolve(111);
  reject(222);
})

promise.then(res => {
  console.log(res)
}, err => {
  console.log(err);
})


```



但是在这里最好不要用setTimeout， 因为在原生的实现上，它用的就是微任务，而我们这里用的是宏任务，所以我们再改造一下

```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  // 它会接收一个参数，这个参数就是一个函数
  constructor(executor) {
    // 默认状态是pending状态
    this.status = PROMISE_STATUS_PENDING;
    // 保存传进来的参数
    this.value = undefined;
    this.reason = undefined;

    // 因为传进来的这个函数是有两个函数，一个是resolve, 一个是reject
    // 所以我们需要在这里实现这两个函数，并且给到executor中
    // 接收参数，这里叫value
      const resolve = (value) => {
        // 只有状态是pending的时候才能调用这个东西
        if(this.status === PROMISE_STATUS_PENDING){
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value;
          queueMicrotask(() => {
            this.onFulfilled(this.value);
          })
          console.log('resolve被调用')
          // 这里要执行then传进来的回调
        }
      }
      // 这里的叫reason
      const reject = (reason) => {
        if(this.status === PROMISE_STATUS_PENDING) {
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason;
          console.log('reject被调用');
          queueMicrotask(() => {
            this.onRejected(this.reason);
          }, 0)
          // 这里要执行then传进来的第二个回调
        }
      }

      // 我们需要调用这个函数
      executor(resolve, reject);
  }
  // 调用.then的时候执行这个函数，并且接收两个函数
  then(onFulfilled, onRejected) {
    // 保存以后去调用
    this.onFulfilled = onFulfilled;
    this.onRejected = onRejected;
  }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  // 传进去的参数必须要保存下来
  resolve(111);
  reject(222);
})

promise.then(res => {
  console.log(res)
}, err => {
  console.log(err);
})


```

queueMicrotask是加在微任务当中，会在本轮的事件循环中进行执行

如果接着在下面调用then,应该是两个then都会执行的，但是我们调用的话，只会执行第二个

![image-20220707065950254](.\18_promise\image-20220707065950254.png)

因为第二个会把第一个覆盖掉

而且我们这里不能做链式调用

![image-20220707070138792](.\18_promise\image-20220707070138792.png)



当前的then方法不支持调用多次，后面把之前的方法调用，then里面的两个方法

![image-20220707070617695](.\18_promise\image-20220707070617695.png)

then方法不支持链式调用



那么怎么做呢？

塞入到数组里面去

```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING;
    this.value = undefined;
    this.reason = undefined;

    // 保存成功的回调函数
    this.onFulfilledFns = [];
    this.onRejectedFns = []
      const resolve = (value) => {
        if(this.status === PROMISE_STATUS_PENDING){
          this.status = PROMISE_STATUS_FULFILLED
          queueMicrotask(() => {
            this.value = value;
            this.onRejectedFns.forEach(fn => {
              fn(this.value);
            })
          })
          console.log('resolve被调用')
        }
      }
      const reject = (reason) => {
        if(this.status === PROMISE_STATUS_PENDING) {
          this.status = PROMISE_STATUS_REJECTED
          queueMicrotask(() => {
            this.reason = reason;
            console.log('reject被调用');
            this.onRejected(this.reason);
            this.onRejectedFns.forEach(fn => {
              fn(this.reason);
            })
          })
        }
      }
      executor(resolve, reject);
  }
  then(onFulfilled, onRejected) {

    // 将成功回调和失败回调加入到数组中
    this.onFulfilledFns.push(onFulfilled);
    this.onRejectedFns.push(onRejected);
  }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  // 传进去的参数必须要保存下来
  resolve(111);
  reject(222);
})

promise.then(res => {
  console.log(res)
  console.log('then1')
}, err => {
  console.log(err);
})

promise.then(res => {
  console.log('then2')
  console.log(res)
}, err => {
  console.log(err);
})



```

![image-20220707071229835](.\18_promise\image-20220707071229835.png)



但是如果promise变成了resolve

![image-20220707071435510](.\18_promise\image-20220707071435510.png)

不会，因为这里已经回调完了

![image-20220707071608119](.\18_promise\image-20220707071608119.png)

我在执行onFulfilledFns的时候，你延迟了一秒钟，但是我已经执行完了，等我执行完了你才加进来，这个时候，我肯定不会再来执行一遍你的



但是原生的promise是可以的

![image-20220707071748205](.\18_promise\image-20220707071748205.png)

这个代码就会直接回调的



那要怎么办呢？

![image-20220707072055130](.\18_promise\image-20220707072055130.png)

优化多次调用，延迟一会再调用

```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING;
    this.value = undefined;
    this.reason = undefined;

    // 保存成功的回调函数
    this.onFulfilledFns = [];
    this.onRejectedFns = []
      const resolve = (value) => {
        if(this.status === PROMISE_STATUS_PENDING){
          queueMicrotask(() => {
            this.status = PROMISE_STATUS_FULFILLED
            this.value = value;
            this.onFulfilledFns.forEach(fn => {
              fn(this.value);
            })
          })
          console.log('resolve被调用')
        }
      }
      const reject = (reason) => {
        if(this.status === PROMISE_STATUS_PENDING) {
          queueMicrotask(() => {
            this.status = PROMISE_STATUS_REJECTED
            this.reason = reason;
            this.onRejectedFns.forEach(fn => {
              fn(this.reason);
            })
          })
        }
      }
      executor(resolve, reject);
  }
  then(onFulfilled, onRejected) {
    if(this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
      onFulfilled(this.value);
    }
    if(this.status === PROMISE_STATUS_REJECTED && onRejected) {
      onRejected(this.reason);
    }

    if(this.status === PROMISE_STATUS_PENDING) {
        this.onFulfilledFns.push(onFulfilled);
        this.onRejectedFns.push(onRejected);
      }
    }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  // 传进去的参数必须要保存下来
  resolve(111);
  // reject(222);
})

promise.then(res => {
  console.log('then1')
  console.log(res)
}, err => {
  console.log(err);
})

promise.then(res => {
  console.log('then2')
  console.log(res)
}, err => {
  console.log(err);
})

setTimeout(() => {
  promise.then(res => {
    console.log('promise3')
    console.log(res);
  })
}, 1000)


```



![image-20220707072649015](.\18_promise\image-20220707072649015.png)

如果这里都执行了，那么他们都会执行

![image-20220707072915256](.\18_promise\image-20220707072915256.png)

这样解决这个问题

```js

const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'
class HTPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING;
    this.value = undefined;
    this.reason = undefined;

    // 保存成功的回调函数
    this.onFulfilledFns = [];
    this.onRejectedFns = []
      const resolve = (value) => {
        if(this.status === PROMISE_STATUS_PENDING){
          queueMicrotask(() => {
            if(this.status !== PROMISE_STATUS_PENDING) return;
            this.status = PROMISE_STATUS_FULFILLED
            this.value = value;
            this.onFulfilledFns.forEach(fn => {
              fn(this.value);
            })
          })
          console.log('resolve被调用')
        }
      }
      const reject = (reason) => {
        if(this.status === PROMISE_STATUS_PENDING) {
          queueMicrotask(() => {
            if(this.status !== PROMISE_STATUS_PENDING) return;
            this.status = PROMISE_STATUS_REJECTED
            this.reason = reason;
            this.onRejectedFns.forEach(fn => {
              fn(this.reason);
            })
          })
        }
      }
      executor(resolve, reject);
  }
  then(onFulfilled, onRejected) {
    if(this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
      onFulfilled(this.value);
    }
    if(this.status === PROMISE_STATUS_REJECTED && onRejected) {
      onRejected(this.reason);
    }

    if(this.status === PROMISE_STATUS_PENDING) {
        this.onFulfilledFns.push(onFulfilled);
        this.onRejectedFns.push(onRejected);
      }
    }
}

// 我们在使用promise的时候是这个样子
const promise = new HTPromise((resolve, reject) => {
  console.log('这里被调用了')
  // 但是有一个问题，如果resolve被调用了，那么实际上reject不能再被调用的，也就是无效的调用
  // 其实在调用executor这个函数的时候，整个promise的状态应该是pendding的状态的，当调用resolve的状态后，它的状态应该是fulfilled，当调用了reject后，它的状态应该是rejected状态，
  // 所以必须记录这个状态
  // 传进去的参数必须要保存下来
  reject(222);
  resolve(111);
})

promise.then(res => {
  console.log('then1')
  console.log(res)
}, err => {
  console.log(err);
})

promise.then(res => {
  console.log('then2')
  console.log(res)
}, err => {
  console.log(err);
})

setTimeout(() => {
  promise.then(res => {
    console.log('promise3')
    console.log(res);
  })
}, 1000)



```



现在我们要做链式调用

![image-20220707073523436](.\18_promise\image-20220707073523436.png)

这样才能链式调用

但是我们是没有返回值的，所以默认返回的是undefined

![image-20220707073606856](.\18_promise\image-20220707073606856.png)

![image-20220707073749138](.\18_promise\image-20220707073749138.png)

但是这里不应该写死的， 而且，应该是第一次有结果的时候，才能拿到这个结果，然后在第二次才能调用resolve



![image-20220707074225567](.\18_promise\image-20220707074225567.png)



```js

// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
}

const promise = new HYPromise((resolve, reject) => {
  console.log("状态pending")
  // resolve(1111) // resolved/fulfilled
  reject(2222)
  // throw new Error("executor error message")
})

// 调用then方法多次调用
promise.then(res => {
  console.log("res1:", res)
  return "aaaa"
  // throw new Error("err message")
}, err => {
  console.log("err1:", err)
  return "bbbbb"
  // throw new Error("err message")
}).then(res => {
  console.log("res2:", res)
}, err => {
  console.log("err2:", err)
})

```



Promise A+要实现的功能，现在已经实现完了

实现catch方法

我们之前的catch是放到then的第二个参数，但是如果我们想这样写，就必须实现一个catch函数

![image-20220709081109342](.\18_promise\image-20220709081109342.png)

我们可以这样写

![image-20220709081347387](.\18_promise\image-20220709081347387.png)

有一个问题

![image-20220709081330166](.\18_promise\image-20220709081330166.png)

因为这里是空的

![image-20220709081425994](.\18_promise\image-20220709081425994.png)

因为undefined会被加入到里面去，执行undefined就报错了

解决

![image-20220709081744868](.\18_promise\image-20220709081744868.png)



继续执行

![image-20220709081727117](.\18_promise\image-20220709081727117.png)

![image-20220709082028799](.\18_promise\image-20220709082028799.png)

所以调不到的

要这样做

![image-20220709082543272](.\18_promise\image-20220709082543272.png)

```js
// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onRejected = onRejected === undefined ? err => {throw err} : onRejected;
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        onFulfilled && this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        onRejected && this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
  catch(onRejected) {
    // 第一个参数不需要传
    this.then(undefined, onRejected)
  }
}

const promise = new HYPromise((resolve, reject) => {
  console.log("状态pending")
  // resolve(1111) // resolved/fulfilled
  reject(2222)
  // throw new Error("executor error message")
})

promise.then(res => {
  console.log('res', res);
}, err => {
  console.log(err)
}).catch(err => {
  console.log(err)
})
```



这样做，就可以让catch实现链式了



实现finally方法

当前不能使用finally方法，因为catch没有return任何东西

![image-20220709083452916](.\18_promise\image-20220709083452916.png)



![image-20220709083531149](.\18_promise\image-20220709083531149.png)

因为这里

![image-20220709083604507](.\18_promise\image-20220709083604507.png)



因为undefined，断层了

![image-20220709083803538](.\18_promise\image-20220709083803538.png)

```js
// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onRejected = onRejected === undefined ? err => {throw err} : onRejected;
      // 这里要加个判断
    onFulfilled = onFulfilled === undefined ? value => {return value} : onFulfilled;
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        onFulfilled && this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        onRejected && this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
  catch(onRejected) {
    // 第一个参数不需要传
    return this.then(undefined, onRejected)
  }
  finally(onFinally) {
    this.then(() => {
      onFinally()
    }, () => {
      onFinally();
    });
  }
}

const promise = new HYPromise((resolve, reject) => {
  console.log("状态pending")
  // resolve(1111) // resolved/fulfilled
  reject(2222)
  // throw new Error("executor error message")
})

promise.then(res => {
  console.log('res', res);
}, err => {
  console.log(err)
}).catch(res => {
  console.log('bbbb')
  return 'bbbbb'
}).finally(() => {
  console.log('finally')
})
```

对象方法已经实现完了

然后实现类方法，resolve和reject

```js
// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onRejected = onRejected === undefined ? err => {throw err} : onRejected;
    onFulfilled = onFulfilled === undefined ? value => {return value} : onFulfilled;
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        onFulfilled && this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        onRejected && this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
  catch(onRejected) {
    // 第一个参数不需要传
    return this.then(undefined, onRejected)
  }
  finally(onFinally) {
    this.then(() => {
      onFinally()
    }, () => {
      onFinally();
    });
  }
  static resolve(value) {
    return new HYPromise(resolve => resolve(value))
  }
  static reject(reason) {
    return new HYPromise((resolve, reject) => reject(reason))
  }
}

const promise = new HYPromise((resolve, reject) => {
  console.log("状态pending")
  // resolve(1111) // resolved/fulfilled
  reject(2222)
  // throw new Error("executor error message")
})
HYPromise.resolve('hello world').then(res => {
  console.log(res)
})
HYPromise.reject('hello world').catch(err => {
  console.log(err)
})
```



all, allSettled方法

```js
// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onRejected = onRejected === undefined ? err => {throw err} : onRejected;
    onFulfilled = onFulfilled === undefined ? value => {return value} : onFulfilled;
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        onFulfilled && this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        onRejected && this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
  catch(onRejected) {
    // 第一个参数不需要传
    return this.then(undefined, onRejected)
  }
  finally(onFinally) {
    this.then(() => {
      onFinally()
    }, () => {
      onFinally();
    });
  }
  static resolve(value) {
    return new HYPromise(resolve => resolve(value))
  }
  static reject(reason) {
    return new HYPromise((resolve, reject) => reject(reason))
  }
  static all(promises) {

    return new HYPromise((resolve, reject) => {
      const values = [];
      promises.forEach(promise => {
        promise.then(res => {
          values.push(res);
          if(values.length === promises.length) {
            resolve(values);
          }
        }, err => {
          reject(err);
        })
      })
    })
  }
}
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(111)
  }, 1000)
})
const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(222)
  }, 2000)
})
const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(333)
  }, 3000)
})

// 等所有的都有结果了之后，才执行then
// 如果有一个是err，那么就执行catch
HYPromise.all([p1, p2, p3]).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})

```

实现了all

```js
// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onRejected = onRejected === undefined ? err => {throw err} : onRejected;
    onFulfilled = onFulfilled === undefined ? value => {return value} : onFulfilled;
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        onFulfilled && this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        onRejected && this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
  catch(onRejected) {
    // 第一个参数不需要传
    return this.then(undefined, onRejected)
  }
  finally(onFinally) {
    this.then(() => {
      onFinally()
    }, () => {
      onFinally();
    });
  }
  static resolve(value) {
    return new HYPromise(resolve => resolve(value))
  }
  static reject(reason) {
    return new HYPromise((resolve, reject) => reject(reason))
  }
  static all(promises) {

    return new HYPromise((resolve, reject) => {
      const values = [];
      promises.forEach(promise => {
        promise.then(res => {
          values.push(res);
          if(values.length === promises.length) {
            resolve(values);
          }
        }, err => {
          reject(err);
        })
      })
    })
  }
  static allSettled(promises) {
    return new HYPromise((resolve, reject) => {
      const results = [];
      promises.forEach(promise => {
        promise.then(res => {
          results.push({status: PROMISE_STATUS_FULFILLED, value: res})
          if(results.length === promises.length) {
            resolve(results)
          }
        }, err => {
          results.push({status: PROMISE_STATUS_REJECTED, value: err})
          if(results.length === promises.length) {
            resolve(results)
          }
        })
      })
    })
  }
}
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(111)
  }, 1000)
})
const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(222)
  }, 2000)
})
const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(333)
  }, 3000)
})

// 等所有的都有结果了之后，才执行then
// 如果有一个是err，那么就执行catch
HYPromise.allSettled([p1, p2, p3]).then(res => {
  console.log(res);
}).catch(err => {
  console.log(err);
})

```

实现了allSettled



race方法

```js
// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onRejected = onRejected === undefined ? err => {throw err} : onRejected;
    onFulfilled = onFulfilled === undefined ? value => {return value} : onFulfilled;
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        onFulfilled && this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        onRejected && this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
  catch(onRejected) {
    // 第一个参数不需要传
    return this.then(undefined, onRejected)
  }
  finally(onFinally) {
    this.then(() => {
      onFinally()
    }, () => {
      onFinally();
    });
  }
  static resolve(value) {
    return new HYPromise(resolve => resolve(value))
  }
  static reject(reason) {
    return new HYPromise((resolve, reject) => reject(reason))
  }
  static all(promises) {

    return new HYPromise((resolve, reject) => {
      const values = [];
      promises.forEach(promise => {
        promise.then(res => {
          values.push(res);
          if(values.length === promises.length) {
            resolve(values);
          }
        }, err => {
          reject(err);
        })
      })
    })
  }
  static allSettled(promises) {
    return new HYPromise((resolve, reject) => {
      const results = [];
      promises.forEach(promise => {
        promise.then(res => {
          results.push({status: PROMISE_STATUS_FULFILLED, value: res})
          if(results.length === promises.length) {
            resolve(results)
          }
        }, err => {
          results.push({status: PROMISE_STATUS_REJECTED, value: err})
          if(results.length === promises.length) {
            resolve(results)
          }
        })
      })
    })
  }
  static race(promises) {
    return new HYPromise((resolve, reject) => {
      promises.forEach(promise => {
        promise.then(res => {
          resolve(res);
        }, err => {
          reject(err);
        })
      })
    })
  }
  static any(promises) {
    
  }
}
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(111)
  }, 4000)
})
const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(222)
  }, 5000)
})
const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(333)
  }, 3000)
})

HYPromise.race([p1, p2, p3]).then(res => {
  console.log(res)
}).catch(err => {
  console.log('err', err)
})

```

 

实现了any方法

```js
// ES6 ES2015
// https://promisesaplus.com/
const PROMISE_STATUS_PENDING = 'pending'
const PROMISE_STATUS_FULFILLED = 'fulfilled'
const PROMISE_STATUS_REJECTED = 'rejected'

// 工具函数
function execFunctionWithCatchError(execFn, value, resolve, reject) {
  try {
    const result = execFn(value)
    resolve(result)
  } catch(err) {
    reject(err)
  }
}

class HYPromise {
  constructor(executor) {
    this.status = PROMISE_STATUS_PENDING
    this.value = undefined
    this.reason = undefined
    this.onFulfilledFns = []
    this.onRejectedFns = []

    const resolve = (value) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_FULFILLED
          this.value = value
          this.onFulfilledFns.forEach(fn => {
            fn(this.value)
          })
        });
      }
    }

    const reject = (reason) => {
      if (this.status === PROMISE_STATUS_PENDING) {
        // 添加微任务
        queueMicrotask(() => {
          if (this.status !== PROMISE_STATUS_PENDING) return
          this.status = PROMISE_STATUS_REJECTED
          this.reason = reason
          this.onRejectedFns.forEach(fn => {
            fn(this.reason)
          })
        })
      }
    }

    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }

  then(onFulfilled, onRejected) {
    onRejected = onRejected === undefined ? err => {throw err} : onRejected;
    onFulfilled = onFulfilled === undefined ? value => {return value} : onFulfilled;
    return new HYPromise((resolve, reject) => {
      // 1.如果在then调用的时候, 状态已经确定下来
      if (this.status === PROMISE_STATUS_FULFILLED && onFulfilled) {
        // try {
        //   const value = onFulfilled(this.value)
        //   resolve(value)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
      }
      if (this.status === PROMISE_STATUS_REJECTED && onRejected) {
        // try {
        //   const reason = onRejected(this.reason)
        //   resolve(reason)
        // } catch(err) {
        //   reject(err)
        // }
        execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
      }

      // 2.将成功回调和失败的回调放到数组中
      if (this.status === PROMISE_STATUS_PENDING) {
        onFulfilled && this.onFulfilledFns.push(() => {
          // try {
          //   const value = onFulfilled(this.value)
          //   resolve(value)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onFulfilled, this.value, resolve, reject)
        })
        onRejected && this.onRejectedFns.push(() => {
          // try {
          //   const reason = onRejected(this.reason)
          //   resolve(reason)
          // } catch(err) {
          //   reject(err)
          // }
          execFunctionWithCatchError(onRejected, this.reason, resolve, reject)
        })
      }
    })
  }
  catch(onRejected) {
    // 第一个参数不需要传
    return this.then(undefined, onRejected)
  }
  finally(onFinally) {
    this.then(() => {
      onFinally()
    }, () => {
      onFinally();
    });
  }
  static resolve(value) {
    return new HYPromise(resolve => resolve(value))
  }
  static reject(reason) {
    return new HYPromise((resolve, reject) => reject(reason))
  }
  static all(promises) {

    return new HYPromise((resolve, reject) => {
      const values = [];
      promises.forEach(promise => {
        promise.then(res => {
          values.push(res);
          if(values.length === promises.length) {
            resolve(values);
          }
        }, err => {
          reject(err);
        })
      })
    })
  }
  static allSettled(promises) {
    return new HYPromise((resolve, reject) => {
      const results = [];
      promises.forEach(promise => {
        promise.then(res => {
          results.push({status: PROMISE_STATUS_FULFILLED, value: res})
          if(results.length === promises.length) {
            resolve(results)
          }
        }, err => {
          results.push({status: PROMISE_STATUS_REJECTED, value: err})
          if(results.length === promises.length) {
            resolve(results)
          }
        })
      })
    })
  }
  static race(promises) {
    return new HYPromise((resolve, reject) => {
      promises.forEach(promise => {
        promise.then(res => {
          resolve(res);
        }, err => {
          reject(err);
        })
      })
    })
  }
  static any(promises) {
    return new HYPromise((resolve, reject) => {
      let reasons = []
      promises.forEach(promise => {
        promise.then(res => {
          resolve(res);
        }, err => {
          reasons.push(err);
          if(reasons.length === promises.length) {
            // 只能在浏览器测试
            reject(new AggregateError(reasons))
          }
        })
      })
    })
  }
}
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(111)
  }, 4000)
})
const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(222)
  }, 5000)
})
const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(333)
  }, 3000)
})

HYPromise.any([p1, p2, p3]).then(res => {
  console.log(res)
}).catch(err => {
  console.log('err', err.errors)
})

```

# 简单总结手写Promise

## 一. Promise规范

https://promisesaplus.com/



## 二. Promise类设计

```js
class HYPromise {}
```

```js
function HYPromise() {}
```



## 三. 构造函数的规划

```js
class HYPromise {
  constructor(executor) {
   	// 定义状态
    // 定义resolve、reject回调
    // resolve执行微任务队列：改变状态、获取value、then传入执行成功回调
    // reject执行微任务队列：改变状态、获取reason、then传入执行失败回调
    
    // try catch
    executor(resolve, reject)
  }
}
```



## 四. then方法的实现

```js
class HYPromise {
  then(onFulfilled, onRejected) {
    // this.onFulfilled = onFulfilled
    // this.onRejected = onRejected
    
    // 1.判断onFulfilled、onRejected，会给默认值
    
    // 2.返回Promise resolve/reject
    
    // 3.判断之前的promise状态是否确定
    // onFulfilled/onRejected直接执行（捕获异常）
    
    // 4.添加到数组中push(() => { 执行 onFulfilled/onRejected 直接执行代码})
  }
}
```



## 五. catch方法

```js
class HYPromise {
  catch(onRejected) {
    return this.then(undefined, onRejected)
  }
}
```



## 六. finally

```js
class HYPromise {
  finally(onFinally) {
    return this.then(() => {onFinally()}, () => {onFinally()})
  }
}
```



## 七. resolve/reject



## 八. all/allSettled

核心：要知道new Promise的resolve、reject在什么情况下执行

all：

* 情况一：所有的都有结果
* 情况二：有一个reject

allSettled：

* 情况：所有都有结果，并且一定执行resolve



## 九.race/any

race:

* 情况：只要有结果

any:

* 情况一：必须等到一个resolve结果
* 情况二：都没有resolve，所有的都是reject





