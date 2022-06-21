## 监听对象的操作

 我们先来看一个需求：有一个对象，我们希望监听这个对象中的属性被设置或获取的过程

比如说

```js
const obj = {name: 'why'}
obj.name = 'kobe';
delete obj.name
// 监听到这个赋值的操作，---> 执行一些其他事情
// 就像vue中，通过监听name,让我们的template中的name进行更新
```

-  通过我们前面所学的知识，能不能做到这一点呢？
- 监听对象操作在很多框架中是比较重要的
-  其实是可以的，我们可以通过之前的属性描述符中的存储属性描述符来做到；

这段代码就利用了前面讲过的 Object.defineProperty 的存储属性描述符来对 属性的操作进行监听。

```js
const obj = {
	name: 'wts',
	age: 18
}

// 上面这两个操作都想监听到
Object.defineProperty(obj, 'name', {
    // 当访问name的时候来到get
    get: function () {
        console.log('监听到name属性被访问了')
    },
    // 当设置name的时候来到set
   set: function(){
       console.log('监听到name属性被设置了')
   } ,
})
// 想要监听这么一个对象
console.log(obj.name);	// 监听到name属性被访问了
obj.name = 'kobe';	// 监听到name属性被设置了

// 上面只是针对name属性做监听，如果想针对所有属性都做监听怎么办呢
Object.keys(obj).forEach(key => {	// 这个方法可以获取到所有的key,这个方法表示会调用所有的key来循环
    let value = obj[key];
    Object.defineProperty(Obje, key, {
        get() {
            console.log('监听到obj对象的' + key + '属性被访问了')
            return value;
        },
        set(newValue){
            console.log('监听到obj对象的' + key+ '属性被设置了')
            value = newValue;	// 通过改变外面的自有变量，来设置
        }
    })	// 在循环中监听每一个key
})
obj.name = 'kobo';
obj.age = 30;
console.log(obj.name);
console.log(obj.age);

// 打印监听多个obj的代码
'监听到obj对象的name属性被设置了'
'监听到obj对象的age属性被设置了'
'监听到obj对象的name属性被访问了'
kobe
'监听到obj对象的age属性被访问了'
30
```



![image-20220621064127846](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621064127846.png)

所以在前面这种方式里面Object.defineProperty里面他也是可以做监听操作的

但是这样做有什么缺点呢？

 首先，Object.defineProperty设计的初衷，不是为了去监听截止一个对象中所有的属性的（属性的变化）。

-  我们在定义某些属性的时候，初衷其实是定义普通的属性，但是后面我们强行将它变成了数据属性描述符。

 其次，如果我们想监听更加丰富的操作，比如新增属性、删除属性，那么 Object.defineProperty是无能为力的。

```js
obj.height = 1.88
// definedProperty是监听不到的
// 或者是删除一个属性  defineProperty也是监听不到的
```



 所以我们要知道，存储数据描述符设计的初衷并不是为了去监听一个完整的对 象。

所以es6提供了一个Proxy



## Proxy基本使用

在ES6中，新增了一个**Proxy类**，这个类从名字就可以看出来，是用于帮助我们创建一个代理的：

也就是说，如果我们希望监听一个对象的相关操作，那么我们可以先创建一个代理对象（Proxy对象）；

- 也就是说我们如果想监听一个对象，我们不应该直接去监听这个对象，我们应该先创建一个代理对象

之后对该对象的所有操作，都通过代理对象来完成，代理对象可以监听我们想要对原对象进行哪些操作；

- 如果我们想监听一个对象，我们其实不应该直接修改这个对象，这种方式其实不好
- 那么Proxy做的事其实就是先创建出一个代理对象
- 如果我们创建出一个代理对象，我们修改的都是代理对象的值，最后把代理对象传递给我们的对象
- 我们可以重写捕获器，一共有13种捕获器

我们可以将上面的案例用Proxy来实现一次：

- 首先，我们需要new Proxy对象，并且传入需要侦听的对象以及一个处理对象，可以称之为handler； ü const p = new Proxy(target, handler)

- 其次，我们之后的操作都是直接对Proxy的操作，而不是原有的对象，因为我们需要在handler里面进行侦听；

  ![image-20220621064318858](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621064318858.png)

```js

const obj = {
  name: 'wts',
  age: 18
}

// 首先要知道对哪个对象做代理,也就是第一个参数
// 第二个参数传的是捕获器，可以捕获到设置对象删除对象等,先给给空对象
const objProxy = new Proxy(obj, {});

console.log(objProxy.name)  // wts
console.log(objProxy.age) // 18
```

可以看到，我们通过proxy对象，也是可以获取到对象的属性的 

```js
objProxy.name = 'kobe';
objProxy.age = 30;
console.log(obj.name);	// kobe
console.log(obj.age);	//30
```

可以看到我们修改Proxy对象，依然可以修改到Obj对象

以上就是Proxy的基本操作

如果我们想监听到设置和获取怎么办呢

就可以重写捕获器



## Proxy的set和get捕获器

如果我们想要侦听某些具体的操作，那么就可以在handler中添加对应的捕捉器（Trap）：

```js
const obj = {
  name: 'wts',
  age: 18
}

// 首先要知道对哪个对象做代理,也就是第一个参数
// 第二个参数传的是捕获器，可以捕获到设置对象删除对象等,先给给空对象
const objProxy = new Proxy(obj, {
  // 获取值时的捕获器
  // 当我们获取值时，就会自动回调这个捕获器
  // 当我们获取属性的时候，其实会传过来好几个参数
  // target表示被代理的对象
  // key代表获取的属性
  get: function (target, key) {
    console.log(`监听到对象的${key}属性被访问了`, target)
    return target[key];
  },

  // 设置值时的捕获器
  // 当我们设置值时，就会自动回调这个捕获器
  // 当我们设置属性的时候，也会传过来好几个参数
  // target表示被代理的对象
  // key代表设置的属性
  // newValue就是修改的新的值
  set: function (target, key, newValue) {
    console.log(`监听到对象的${key}属性被设置了`, target);
    target[key] = newValue;
  }
});

console.log(objProxy.name)  // 监听到对象的name属性被访问了 { name: 'wts', age: 18 }	wts
console.log(objProxy.age) // 监听到对象的age属性被访问了 { name: 'wts', age: 18 }	18

objProxy.name = 'kobe';	// 监听到对象的name属性被设置了 { name: 'wts', age: 18 }
objProxy.age = 30;	// 监听到对象的age属性被设置了 { name: 'kobe', age: 18 }
console.log(obj.name);	// kobe
console.log(obj.age);	// 30

```



set和get分别对应的是函数类型；

- set函数有四个参数：
  - target：目标对象（侦听的对象）；
  - property：将被设置的属性key；
  - value：新属性值；
  - receiver：调用的代理对象；
- get函数有三个参数：
  - target：目标对象（侦听的对象）；
  - property：被获取的属性key；
  - receiver：调用的代理对象；

![image-20220621064459699](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621064459699.png)



## Proxy所有捕获器

### 13个活捉器分别是做什么的呢？



举个栗子，假设我们用 *in* 操作符的时候，在以前用defineProperty的时候，我们是不可能监听到的，那么用Proxy能不能监听到呢

```js
let obj = {name: 'wts', age: 18}
const objProxy = new Proxy(obj, {
    get: () {},
    set: () {},
    // 监听in的捕获器, 他只有两个参数，一个target，一个key，没有recever，recever只有get和set有
    has: (target, key) {
        console.log('监听到使用了in操作符')
        // 这里需要return一下
        return key in target
    },
    // 监听delete的捕获器
    deleteProperty: function (target, key) {
        console.log('监听到对象的' + key + '属性in操作', target)
        delete target[key]; 
    }
})

// in操作
console.log('name' in objProxy);

// delete操作
delete objProxy.name
```

所以，对于proxy不仅仅对以前的监听方式做了一个增强，更重要的是对某一个对象做了一个代理，我们只需要操作代理对象就可以， 这样的话就不需要直接修改我们的被代理对象了

如果修改直接通过Object.defineProperty（obj, 'name', {})来修改对象的话，我们是把他的属性修饰符给改掉了，

比如

```js
const obj = {
name: 'wts'	// 数据属性描述符
}
Object.defineProperty(obj, 'name', {})	// 变成成了访问属性描述符
```

所以这种方式不是特别好

所以现在我们可以通过proxy来创建一个代理对象



### handler.getPrototypeOf()

- Object.getPrototypeOf 方法的捕捉器。



### handler.setPrototypeOf()

- Object.setPrototypeOf 方法的捕捉器。



### handler.isExtensible()

- Object.isExtensible 方法的捕捉器。



### handler.preventExtensions()

- Object.preventExtensions 方法的捕捉器。



### handler.getOwnPropertyDescriptor()

- Object.getOwnPropertyDescriptor 方法的捕捉器。



### handler.defineProperty()

- Object.defineProperty 方法的捕捉器。



### handler.ownKeys()

- Object.getOwnPropertyNames 方法和 Object.getOwnPropertySymbols 方法的捕捉器。



### handler.has()

- in 操作符的捕捉器。



### handler.get()

- 属性读取操作的捕捉器。



### handler.set()

- 属性设置操作的捕捉器。



### handler.deleteProperty()

- delete 操作符的捕捉器。



### handler.apply()

- 函数调用操作的捕捉器。
- 用于函数对象

```js
function foo() {}
const fooProxy = new Proxy(foo, {
	// 第一个参数是foo函数，第二个参数是this对象，第三个参数是apply时传递的参数
	apply: function (target, thisAry, argArray) {
		console.log('对foo函数进行了监听');
		target.apply(thisArg, argArry)
	}， 
    // 其实target和newTarget是一样的
    constructor: function (target, argArray, newTarget) {
    	console.log('监听到new调用')
    	return new target(...argArray);
	}
})
fooProxy.apply({}, ['abc', 'cba']);
new fooProxy('abc', 'cba')
```





### handler.construct()

- new 操作符的捕捉器。
- 用于函数对象







## Proxy的construct和apply

当然，我们还会看到捕捉器中还有construct和apply，它们是应用于函数对象的：

![image-20220621072951948](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621072951948.png)





## Reflect的作用

Reflect也是ES6新增的一个API，它是一个对象，字面的意思是反射。

proxy经常和reflect一起来使用

那么这个Reflect有什么用呢？

- 它主要提供了很多操作JavaScript对象的方法，有点像Object中操作对象的方法；
- 比如Reflect.getPrototypeOf(target)类似于 Object.getPrototypeOf()；
- 比如Reflect.defineProperty(target, propertyKey, attributes)类似于Object.defineProperty() ；

如果我们有Object可以做这些操作，那么为什么还需要有Reflect这样的新增对象呢？

- 这是因为在早期的ECMA规范中没有考虑到这种对 对象本身 的操作如何设计会更加规范，所以将这些API放到了Object上面；
- 但是Object作为一个构造函数，这些操作实际上放到它身上并不合适；
- 另外还包含一些类似于 in、delete操作符，让JS看起来是会有一些奇怪的；
- 所以在ES6中新增了Reflect，让我们这些操作都集中到了Reflect对象上；

那么Object和Reflect对象之间的API关系，可以参考MDN文档：

```
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect/Comparing_Reflect_and_Object_methods
```





## Reflect的常见方法

Reflect中有哪些常见的方法呢？它和Proxy是一一对应的，也是13个：



### Reflect.getPrototypeOf(target)

- 类似于 Object.getPrototypeOf()。



### Reflect.setPrototypeOf(target, prototype)

- 设置对象原型的函数. 返回一个 Boolean， 如果更新成功，则返 回true。



### Reflect.isExtensible(target)

- 类似于 Object.isExtensible()



### Reflect.preventExtensions(target)

- 类似于 Object.preventExtensions()。返回一个Boolean。



### Reflect.getOwnPropertyDescriptor(target, propertyKey)

- 类似于 Object.getOwnPropertyDescriptor()。如果对象中存在 该属性，则返回对应的属性描述符, 否则返回 undefined.



### Reflect.defineProperty(target, propertyKey, attributes)

- 和 Object.defineProperty() 类似。如果设置成功就会返回 true



### Reflect.ownKeys(target)

- 返回一个包含所有自身属性（不包含继承属性）的数组。(类似于 Object.keys(), 但不会受enumerable影响).



### Reflect.has(target, propertyKey)

- 判断一个对象是否存在某个属性，和 in 运算符 的功能完全相同。



### Reflect.get(target, propertyKey[, receiver])

- 获取对象身上某个属性的值，类似于 target[name]。



### Reflect.set(target, propertyKey, value[, receiver])

- 将值分配给属性的函数。返回一个Boolean，如果更新成功，则返回true。



### Reflect.deleteProperty(target, propertyKey)

- 作为函数的delete操作符，相当于执行 delete target[name]。



### Reflect.apply(target, thisArgument, argumentsList)

- 对一个函数进行调用操作，同时可以传入一个数组作为调用参数。和 Function.prototype.apply() 功能类似。



### Reflect.construct(target, argumentsList[, newTarget])

- 对构造函数进行 new 操作，相当于执行 new target(...args)。



## Reflect的使用

之前我们操作代理对象的时候都是通过操作源对象的

```js
get: function (target, key, receiver) {
	return target[key]
}
```

我们本来的目的是不要直接操作源对象的，但是现在就是操作了

其实可以这样做

```js
const objProxy = new Proxy(obj, {
	get: function (target, key, receiver) {
		// 我们就可以这样写，通过Reflect来获取值, 而不是通过target[key]
		return Reflect.get(target, key);
	},
    set: function (target, key, newValue, receiver) {
        return Reflect.set(target, key, newValue);
    }
})
```

那么两种方式有什么区别呢？

一方面是Reflect没有操作源对象

另一方面

```js
target[key] = newValue	// 并不会告诉你是否设置成功，（Object.freeze()， 假如被冻结，就设置不成功)
const result = Reflect.set(target, key, newValue)	// 他会返回一个boolean，告诉你是否设置成功
```





那么我们可以将之前Proxy案例中对原对象的操作，都修改为Reflect来操作：

![image-20220621073625744](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073625744.png)





## Receiver的作用

我们发现在使用getter、setter的时候有一个receiver的参数，它的作用是什么呢？

- 如果我们的源对象（obj）有setter、getter的访问器属性，那么可以通过receiver来改变里面的this；

我们来看这样的一个对象：

![image-20220621073702994](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073702994.png)





## Reflect的construct

![image-20220621073722592](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073722592.png)





## 什么是响应式？

我们先来看一下响应式意味着什么？我们来看一段代码：

- m有一个初始化的值，有一段代码使用了这个值；
- 那么在m有一个新的值时，这段代码可以自动重新执行；

![image-20220621073757393](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073757393.png)

上面的这样一种可以自动响应数据变量的代码机制，我们就称之为是响应式的。

- 那么我们再来看一下对象的响应式：

![image-20220621073820781](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073820781.png)





## 响应式函数设计

首先，执行的代码中可能不止一行代码，所以我们可以将这些代码放到一个函数中：

- 那么我们的问题就变成了，当数据发生变化时，自动去执行某一个函数；

![image-20220621073852479](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073852479.png)



但是有一个问题：在开发中我们是有很多的函数的，我们如何区分一个函数需要响应式，还是不需要响应式呢？

- 很明显，下面的函数中 foo 需要在obj的name发生变化时，重新执行，做出相应；
- bar函数是一个完全独立于obj的函数，它不需要执行任何响应式的操作；

![image-20220621073919680](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073919680.png)

![image-20220621073931775](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621073931775.png)





## 响应式函数的实现watchFn

但是我们怎么区分呢？

- 这个时候我们封装一个新的函数watchFn；
- 凡是传入到watchFn的函数，就是需要响应式的；
- 其他默认定义的函数都是不需要响应式的；

![image-20220621074013097](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074013097.png)

![image-20220621074023284](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074023284.png)



## 响应式依赖的收集

目前我们收集的依赖是放到一个数组中来保存的，但是这里会存在数据管理的问题：

- 我们在实际开发中需要监听很多对象的响应式；
- 这些对象需要监听的不只是一个属性，它们很多属性的变化，都会有对应的响应式函数；
- 我们不可能在全局维护一大堆的数组来保存这些响应函数；

所以我们要设计一个类，这个类用于管理某一个对象的某一个属性的所有响应式函数：

- 相当于替代了原来的简单 reactiveFns 的数组；

![image-20220621074117839](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074117839.png)

![image-20220621074126179](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074126179.png)



## 监听对象的变化

那么我们接下来就可以通过之前学习的方式来监听对象的变量：

- 方式一：通过 Object.defineProperty的方式（vue2采用的方式）；
- 方式二：通过new Proxy的方式（vue3采用的方式）；

我们这里先以Proxy的方式来监听：

![image-20220621074213111](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074213111.png)





## 对象的依赖管理

我们目前是创建了一个Depend对象，用来管理对于name变化需要监听的响应函数：

- 但是实际开发中我们会有不同的对象，另外会有不同的属性需要管理；
- 我们如何可以使用一种数据结构来管理不同对象的不同依赖关系呢？

在前面我们刚刚学习过WeakMap，并且在学习WeakMap的时候我讲到了后面通过WeakMap如何管理这种响应 式的数据依赖：

![image-20220621074306532](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074306532.png)





## 对象依赖管理的实现

我们可以写一个getDepend函数专门来管理这种依赖关系：

![image-20220621074339806](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074339806.png)

![image-20220621074351216](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074351216.png)





## 正确的依赖收集

我们之前收集依赖的地方是在 watchFn 中：

- 但是这种收集依赖的方式我们根本不知道是哪一个key的哪一个depend需要收集依赖；
- 你只能针对一个单独的depend对象来添加你的依赖对象；

那么正确的应该是在哪里收集呢？应该在我们调用了Proxy的get捕获器时

- 因为如果一个函数中使用了某个对象的key，那么它应该被收集依赖；

![image-20220621074445677](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074445677.png)

![image-20220621074455638](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074455638.png)



## 对Depend重构

但是这里有两个问题：

- 问题一：如果函数中有用到两次key，比如name，那么这个函数会被收集两次；
- 问题二：我们并不希望将添加reactiveFn放到get中，以为它是属于Dep的行为；

所以我们需要对Depend类进行重构：

- 解决问题一的方法：不使用数组，而是使用Set；
- 解决问题二的方法：添加一个新的方法，用于收集依赖；

![image-20220621074555326](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074555326.png)

![image-20220621074606594](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074606594.png)



## 创建响应式对象

我们目前的响应式是针对于obj一个对象的，我们可以创建出来一个函数，针对所有的对象都可以变成响应式对象：

![image-20220621074630406](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074630406.png)

![image-20220621074640348](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074640348.png)



## Vue2响应式原理

我们前面所实现的响应式的代码，其实就是Vue3中的响应 式原理：

- Vue3主要是通过Proxy来监听数据的变化以及收集相关 的依赖的；
- Vue2中通过我们前面学习过的Object.defineProerty 的方式来实现对象属性的监听；

我们可以将reactive函数进行如下的重构：

- 在传入对象时，我们可以遍历所有的key，并且通过属 性存储描述符来监听属性的获取和修改；
- 在setter和getter方法中的逻辑和前面的Proxy是一致的；

![image-20220621074745774](D:\studyMaterial\JS高级\笔记\17-Proxy-Reflect\image-20220621074745774.png)

