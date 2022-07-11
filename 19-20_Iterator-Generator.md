## 什么是迭代器？

迭代器（iterator），是确使用户可在容器对象（container，例如链表或数组）上遍访的对象，使用该接口无需关心对象的内部实现细节。

- 其行为像数据库中的光标，迭代器最早出现在1974年设计的CLU编程语言中；
- 在各种编程语言的实现中，迭代器的实现方式各不相同，但是基本都有迭代器，比如Java、Python等；

从迭代器的定义我们可以看出来，迭代器是帮助我们对某个数据结构进行遍历的一个对象。

迭代器本身就是一个对象

在JavaScript中，迭代器也是一个具体的对象，这个对象需要符合迭代器协议（iterator protocol）：

- 迭代器协议定义了产生一系列值（无论是有限还是无限个）的标准方式；

- 那么在js中这个标准就是一个特定的next方法；

next方法有如下的要求：

- 一个无参数或者一个参数的函数，返回一个应当拥有以下两个属性的对象：
- done（boolean）
  - 如果迭代器可以产生序列中的下一个值，则为 false。（这等价于没有指定 done 这个属性。）
  - 如果迭代器已将序列迭代完毕，则为 true。这种情况下，value 是可选的，如果它依然存在，即为迭代结束之后的默认返回值。
- value
  - 迭代器返回的任何 JavaScript 值。done 为 true 时可省略。





## 迭代器的代码练习

![image-20220711065154638](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711065154638.png)

![image-20220711065205203](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711065205203.png)



```js
const iterator = {
  next: function () {
    return {
      done: true,
      value: 123
    }
  }
}
```

这个就是一个迭代器对象

但是这个迭代器不能迭代对象

假如我想迭代一个对象

```js
const names = ['abc', 'cba', 'nba'];
// 以前我们可能是这样遍历它
for(let i = 0; i < names.length; i++) {
    console.log(names[i])
}
// 通过另外一种方案来遍历
// 伪代码
const namesIterator = {
    next: function () {
        return {
            done: false,
            value: 'abc'
        }
        return {
            done: false,
            value: 'cba'
        }
        return {
            done: false,
            value: 'nba'
        }
        return {
            done: true,	// 访问完了以后才为true
            value: undefined	// 这里是undefined
        }
        
    }
}

// 数组本身就是一个可迭代对象
const iterator = names[Symbol.iterator]();	// 拿到迭代器对象
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

![image-20220711071250049](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711071250049.png)

当访问完了以后，再访问的话，value就是undefined, 并且done是true



```js
// 创建一个迭代器对象来访问数组
let index = 0;
const namesIterator = {
    next: function () {
        if(index < names.length) {
            return {done: false, value: names[index++]}
        }else {
            return {done: true, value: undefined}
        }
    }
}

console.log(namesIterator.next()); 
console.log(namesIterator.next()); 
console.log(namesIterator.next()); 
console.log(namesIterator.next()); // {done: true, value: undefined
```

![image-20220711071734811](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711071734811.png)

这样我们就实现了一个迭代器对象，可以将数组进行迭代



上面写的迭代器是一个names的迭代器，也就是说，这个迭代器只能针对前面的names这个对象

那如果有另外的对象呢？

比如

```js
const nums = [10, 20, 30, 40];
```

我们可能又要写一个类似上面的迭代器

那么我们可以写一个针对所有数组的迭代器

```js


const names = ['abc', 'cba', 'nba'];
const nums = [10, 20, 30, 40];
function createArrayIterator(arr) {
    let index = 0;
    return {
        next: function () {
            if(index < arr.length) {
                return {done: false, value： arr[index++]}
            }else {
                return {done: true, value undefined}
            }
        }
    }
}

const namesIterator = createArrayIterator(names);	// 拿到names的迭代器对象
console.log(namesIterator.next());
console.log(namesIterator.next());
console.log(namesIterator.next());
console.log(namesIterator.next());

const numsIterator = createArrayIterator(nums) // 拿到nums的迭代器对象
console.log(numsIterator.next());
console.log(numsIterator.next());
console.log(numsIterator.next());
console.log(numsIterator.next());

```

那么上面的函数，就可以帮助我们生成一个迭代器对象了

但是这些迭代器都是一个有限的迭代器，但是我们也可以创建无限的迭代器

```js
function createNumberIterator(){
    let index = 0
    return {
        next: function () {
            return {
                done: false, value: index++
            }
        }
    }
}
// 这就是一个无限的迭代器，当你的done永远不可能为true的时候，他就是一个无限的迭代器
```

![image-20220711074624446](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074624446.png)

不管调多少次next，他都是false，这个时候，这个迭代器，他就是一个无限的迭代器

无限的迭代器是比较少的，因为迭代器出现的目的就是为了迭代一个容器对象，这个容器对象无限的话，还是比较少的





## 可迭代对象

但是上面的代码整体来说看起来是有点奇怪的：

- 我们获取一个数组的时候，需要自己创建一个index变量，再创建一个所谓的迭代器对象；
- 事实上我们可以对上面的代码进行进一步的封装，让其变成一个可迭代对象；

什么又是可迭代对象呢？

- 它和迭代器是不同的概念；
- 当一个对象实现了iterable protocol协议时，它就是一个可迭代对象；
- 这个对象的要求是必须实现 @@iterator 方法，在代码中我们使用 Symbol.iterator 访问该属性；

当我们要问一个问题，我们转成这样的一个东西有什么好处呢？

- 当一个对象变成一个可迭代对象的时候，进行某些迭代操作，比如 for...of 操作时，其实就会调用它的 @@iterator 方法；

![image-20220712064835100](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220712064835100.png)

把这三个东西封装到一个对象里面，这个对象称之为可迭代对象

首先前面我们知道

迭代器是一个对象

符合迭代器协议 (iterator protocol)



![image-20220712065252370](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220712065252370.png)

迭代器和可迭代对象的区别就是迭代器实现了next方法，next方法返回一个对象，这个对象包含value和iterator

可迭代对象实现了一个方法加Symbol.iterator返回一个迭代器

```js


const iterableObj = {
  names: ['abc', 'cba', 'nba'],
  [Symbol.iterator]: function () {
    let index = 0;
    return {
      // 这个函数需要是一个箭头函数，里面的this才指向iterableObj这个对象
      next: () => {
        if(index < this.names.length) {
          return {done: false, value: this.names[index++]};
        }else {
          return {done: true, value: undefined};
        }
      }
    }
  }
}


// iterableObj对象就是一个可迭代对象
console.log(iterableObj[Symbol.iterator])

// 获得迭代器
let iterator = iterableObj[Symbol.iterator]();
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());

// 每次调用都是生成一个新的迭代器
let iterator1 = iterableObj[Symbol.iterator];

```

上面实现了一个可迭代对象

变成一个可迭代对象有什么用呢？



for...of可以遍历的东西必须是一个可迭代对象

```js
const obj = {
    name: 'why',
    age: 18
}

// 假设有一个对象，我们想用for...of来遍历
// 对象是不支持for...of 的，
// 为什么不支持呢
// 因为对象不是可迭代对象, 所以不支持
for(const item of obj) {
    
}

// 那如果把我们上面封装的对象放到for of里面呢
for(const item of iterableObj) {
    console.log(item)	// 可以打印
}
```

![image-20220712071004129](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220712071004129.png)

当done为true的是否就自动停止







## 可迭代对象的代码

![image-20220711073626493](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711073626493.png)





## 原生迭代器对象

事实上我们平时创建的很多原生对象已经实现了可迭代协议，会生成一个迭代器对象的：

- String、Array、Map、Set、arguments对象、NodeList集合；

![image-20220711073700762](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711073700762.png)

![image-20220711073711394](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711073711394.png)

```js
const names = ['abc', 'cba', 'nba']	// 这个数组实际上是由 new Array创建的
// 这个对象里面实际上就有 names[Symbol.iterator]
```

![image-20220712072325724](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220712072325724.png)

它是一个函数

我们可以获取它的迭代器

![image-20220712072454835](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220712072454835.png)

所以数组本身就是一个可迭代对象

这就解释了，为什么for...of可以遍历数组

```js
// map/set
const set = new Set()
set.add(10)
set.add(100)
for(const item of set){
console.log(item)
}
```

所以，set和map之所以能通过for...of来遍历也是因为它是可迭代对象

```js
console.log(set[Symbol.iterator])	// 他也是一个函数，返回值是一个迭代器
```

函数中的arguments也是一个可迭代对象

```js
function foo(x, y, z){
    console.log(arguments[Symbol.iterator])
    for(const item of arguments){
        console.log(item)
    }
}
foo(10, 20, 30)
```







## 可迭代对象的应用

那么这些东西可以被用在哪里呢？

- JavaScript中语法：for ...of、展开语法（spread syntax）、yield*（后面讲）、解构赋值（Destructuring_assignment）；
- 创建一些对象时：new Map([Iterable])、new WeakMap([iterable])、new Set([iterable])、new WeakSet([iterable]);
- 一些方法的调用：Promise.all(iterable)、Promise.race(iterable)、Array.from(iterable);

![image-20220711073817242](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711073817242.png)

![image-20220711073828562](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711073828562.png)

```js
// 展开语法
const iterableObj = {
    names: ['abc', 'cba', 'nba'],
    [Symbole.iterator]: function() {
        let index = 0;
        return {
            next: () => {
                if(index < this.names.length) {
                    return {
                        done: false, 
                        value: this.names[index++]
                    }
                }else {
                    return {
                        done: true,
                        value: undefined
                    }
                }
            }
        }
    }
}

const names = ['abc', 'cba', 'nba'];	
// 正常来讲我们可以对names做展开运算符，但是其实之所以能对它做展开运算符的操作就是因为它是一个可迭代对象，那我们自己实现的可迭代对象实际上也可以做展开运算，也是因为我们实现了可迭代对象的协议
const newNames = [...names, ...iterableObj];
```



```js
const obj  = {name: 'why', age: 18}
for(const item of obj){
    
} // 对象是不可以for遍历的
const newObj = {...obj}	// 对象是可以进行展开运算符的

// 那么为什么对象不可以通过for遍历，却可以使用展开运算符呢
// ES9(ES2018) 新增了一个特性，要求在对象中是可以使用展开运算符的
// 但是在迭代器中，是不可以使用展开运算符的，所以它的底层压根使用的就不是迭代器


```







## 自定义类的迭代

在前面我们看到Array、Set、String、Map等类创建出来的对象都是可迭代对象：

- 在面向对象开发中，我们可以通过class定义一个自己的类，这个类可以创建很多的对象：
- 如果我们也希望自己的类创建出来的对象默认是可迭代的，那么在设计类的时候我们就可以添加上 @@iterator 方法；

案例：创建一个classroom的类

- 教室中有自己的位置、名称、当前教室的学生；
- 这个教室可以进来新学生（push）；
- 创建的教室对象是可迭代对象；





## 自定义类的迭代实现

![image-20220711073955950](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711073955950.png)

![image-20220711074009661](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074009661.png)





## 迭代器的中断

迭代器在某些情况下会在没有完全迭代的情况下中断：

- 比如遍历的过程中通过break、continue、return、throw中断了循环操作；
- 比如在解构的时候，没有解构所有的值；

那么这个时候我们想要监听中断的话，可以添加return方法：

![image-20220711074056080](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074056080.png)

![image-20220711074106484](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074106484.png)



## 什么是生成器？

生成器是ES6中新增的一种函数控制、使用的方案，它可以让我们更加灵活的控制函数什么时候继续执行、暂停执 行等。

平时我们会编写很多的函数，这些函数终止的条件通常是返回值或者发生了异常。

生成器函数也是一个函数，但是和普通的函数有一些区别：

- 首先，生成器函数需要在function的后面加一个符号：*
- 其次，生成器函数可以通过yield关键字来控制函数的执行流程：
- 最后，生成器函数的返回值是一个Generator（生成器）：
  - 生成器事实上是一种特殊的迭代器；
  - MDN：Instead, they return a special type of iterator, called a Generator.





## 生成器函数执行

我们发现上面的生成器函数foo的执行体压根没有执行，它只是返回了一个生成器对象。

- 那么我们如何可以让它执行函数中的东西呢？调用next即可；
- 我们之前学习迭代器时，知道迭代器的next是会有返回值的；
- 但是我们很多时候不希望next返回的是一个undefined，这个时候我们可以通过yield来返回结果；

![image-20220711074303792](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074303792.png)



![image-20220711074317080](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074317080.png)







## 生成器传递参数 – next函数

函数既然可以暂停来分段执行，那么函数应该是可以传递参数的，我们是否可以给每个分段来传递参数呢？

- 答案是可以的；
- 我们在调用next函数的时候，可以给它传递参数，那么这个参数会作为上一个yield语句的返回值；
- 注意：也就是说我们是为本次的函数代码块执行提供了一个值；

![image-20220711074411111](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074411111.png)





## 生成器提前结束 – return函数

还有一个可以给生成器函数传递参数的方法是通过return函数：

- return传值后这个生成器函数就会结束，之后调用next不会继续生成值了；

![image-20220711074834662](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074834662.png)



## 生成器抛出异常 – throw函数

除了给生成器函数内部传递参数之外，也可以给生成器函数内部抛出异常：

- 抛出异常后我们可以在生成器函数中捕获异常；
- 但是在catch语句中不能继续yield新的值了，但是可以在catch语句外使用yield继续中断函数的执行；

![image-20220711074912766](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074912766.png)

![image-20220711074922038](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074922038.png)



## 生成器替代迭代器

我们发现生成器是一种特殊的迭代器，那么在某些情况下我们可以使用生成器来替代迭代器：

![image-20220711074949285](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711074949285.png)

![image-20220711075002480](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075002480.png)



事实上我们还可以使用yield*来生产一个可迭代对象：

- 这个时候相当于是一种yield的语法糖，只不过会依次迭代这个可迭代对象，每次迭代其中的一个值；

![image-20220711075032337](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075032337.png)





## 自定义类迭代 – 生成器实现

在之前的自定义类迭代中，我们也可以换成生成器：

![image-20220711075101338](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075101338.png)



## 对生成器的操作

既然生成器是一个迭代器，那么我们可以对其进行如下的操作：

![image-20220711075136259](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075136259.png)





## 异步处理方案

学完了我们前面的Promise、生成器等，我们目前来看一下异步代码的最终处理方案。

需求：

- 我们需要向服务器发送网络请求获取数据，一共需要发送三次请求；
- 第二次的请求url依赖于第一次的结果；
- 第三次的请求url依赖于第二次的结果；
- 依次类推；

![image-20220711075237143](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075237143.png)

![image-20220711075250749](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075250749.png)

![image-20220711075302953](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075302953.png)





## Generator方案

但是上面的代码其实看起来也是阅读性比较差的，有没有办法可以继续来对上面的代码进行优化呢？

![image-20220711075327704](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075327704.png)

![image-20220711075339021](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075339021.png)





## 自动执行generator函数

目前我们的写法有两个问题：

- 第一，我们不能确定到底需要调用几层的Promise关系；
- 第二，如果还有其他需要这样执行的函数，我们应该如何操作呢？

所以，我们可以封装一个工具函数execGenerator自动执行生成器函数：

![image-20220711075432481](D:\studyMaterial\JS高级\笔记\19-20_Iterator-Generator\image-20220711075432481.png)