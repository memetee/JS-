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

![image-20220711065154638](.\19_Iterator-Generator\image-20220711065154638.png)

![image-20220711065205203](.\19_Iterator-Generator\image-20220711065205203.png)



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

![image-20220711071250049](.\19_Iterator-Generator\image-20220711071250049.png)

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

![image-20220711071734811](.\19_Iterator-Generator\image-20220711071734811.png)

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

![image-20220711074624446](.\19_Iterator-Generator\image-20220711074624446.png)

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

![image-20220712064835100](.\19_Iterator-Generator\image-20220712064835100.png)

把这三个东西封装到一个对象里面，这个对象称之为可迭代对象

首先前面我们知道

迭代器是一个对象

符合迭代器协议 (iterator protocol)



![image-20220712065252370](.\19_Iterator-Generator\image-20220712065252370.png)

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

![image-20220712071004129](.\19_Iterator-Generator\image-20220712071004129.png)

当done为true的是否就自动停止







## 可迭代对象的代码

![image-20220711073626493](.\19_Iterator-Generator\image-20220711073626493.png)





## 原生迭代器对象

事实上我们平时创建的很多原生对象已经实现了可迭代协议，会生成一个迭代器对象的：

- String、Array、Map、Set、arguments对象、NodeList集合；

![image-20220711073700762](.\19_Iterator-Generator\image-20220711073700762.png)

![image-20220711073711394](.\19_Iterator-Generator\image-20220711073711394.png)

```js
const names = ['abc', 'cba', 'nba']	// 这个数组实际上是由 new Array创建的
// 这个对象里面实际上就有 names[Symbol.iterator]
```

![image-20220712072325724](.\19_Iterator-Generator\image-20220712072325724.png)

它是一个函数

我们可以获取它的迭代器

![image-20220712072454835](.\19_Iterator-Generator\image-20220712072454835.png)

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

![image-20220711073817242](.\19_Iterator-Generator\image-20220711073817242.png)

![image-20220711073828562](.\19_Iterator-Generator\image-20220711073828562.png)

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
    
} // 对象是不可以for...of遍历的
const newObj = {...obj}	// 对象是可以进行展开运算符的,但是用的不是迭代器

// 那么为什么对象不可以通过for遍历，却可以使用展开运算符呢
// ES9(ES2018) 新增了一个特性，要求在对象中是可以使用展开运算符的
// 但是在迭代器中，是不可以使用展开运算符的，所以它的底层压根使用的就不是迭代器


```



解构语法

```js
const [name1, name2] = names 	// 迭代器实现的
const {name, age} = obj	// 非迭代器实现的，新增的特性
```



创建一些其他对象时

 ![image-20220713065052746](.\19_Iterator-Generator\image-20220713065052746.png)

让我们传入的是可迭代对象

```js
const set1 = new Set(iterableObj);
const set new Set([1,  2, 3]);
// 上面都是可迭代对象

const arr1 = Array.from(arguments)	// 将arguments转成数组
// Array.from接收的也是一个可迭代对象

// promise.all 接收的也是也是一个可迭代对象，它会将每一个值包裹成promise，然后执行.then
Promise.all(iterableObj).then(res => {
    console.log(res)
})
```

这些就是常见的可迭代对象场景





## 自定义类的迭代

在前面我们看到Array、Set、String、Map等类创建出来的对象都是可迭代对象：

- 在面向对象开发中，我们可以通过class定义一个自己的类，这个类可以创建很多的对象：
- 如果我们也希望自己的类创建出来的对象默认是可迭代的，那么在设计类的时候我们就可以添加上 @@iterator 方法；

案例：创建一个classroom的类

- 教室中有自己的位置、名称、当前教室的学生；
- 这个教室可以进来新学生（push）；
- 创建的教室对象是可迭代对象；

```js
class Person{
    
}
const p1 = new Person()
const p2 = new Person()
const p3 = new Person()

for(const item of p1){}
// 我们自己创建的类不是一个可迭代对象
//但是我们希望它是可迭代对象
```



```js
class ClassRoom{
  constructor(address, name, students) {
      this.address = address;
      this.anme = name;
      this.students = students;
  }
  entry(newStudent) {
      this.students.push(newStudent)
  }

  // 所以要这样写
  [Symbol.iterator]() {
    let index = 0;
    return {
      next:() => {
        if(index < this.students.length) {
          return {
            value: this.students[index++],
            done: false
          }
        }else {
          return{
            value: undefined,
            done: true
          }
        }
      }
    }
  }
}
const classRoom = new ClassRoom('3幢5楼200', '计算机教室', ['james', 'curry', 'wts'])
classRoom.entry('lilei');

for(const item of classroom){}  // 不是可迭代对象

// 逻辑是这样，但是有一个弊端，假如创建多个实例呢？又要写一遍吗
classRoom[Symbol.iterator] = function () {
  return {
      next: function () {
          
      }
  }
}
```



这样就把class类变成了可迭代对象

![image-20220713071401549](.\19_Iterator-Generator\image-20220713071401549.png)



假如我们在某种情况下把迭代器停掉

```js
for(const stu of classRoom) {
    console.log(stu);
    if(stu == 'why') break;
}
// 我们怎么监听已经停掉了呢？
// 因为是迭代器停掉了，所以应该在迭代器进行监听的

  // 所以要这样写
  [Symbol.iterator]() {
    let index = 0;
    return {
      next:() => {
        if(index < this.students.length) {
          return {
            value: this.students[index++],
            done: false
          }
        }else {
          return{
            value: undefined,
            done: true
          }
        }
      },
      return: () => {
        console.log('迭代器提前终止了')
      }
    }
  }
```

但是这种写法还是会报错的

![image-20220713071804343](.\19_Iterator-Generator\image-20220713071804343.png)

是因为这个return函数是有要求的，要求我们的return对象返回的应该也是一个对象，并且有done和value

```js
return: () => {
    console.log('迭代器停止了')
    return {done: true, value: undefined}
}
```

![image-20220713072044960](.\19_Iterator-Generator\image-20220713072044960.png)

这样，迭代器就可以监听到了

```js
class ClassRoom{
  constructor(address, name, students) {
      this.address = address;
      this.anme = name;
      this.students = students;
  }
  entry(newStudent) {
      this.students.push(newStudent)
  }

  // 所以要这样写
  [Symbol.iterator]() {
    let index = 0;
    return {
      next:() => {
        if(index < this.students.length) {
          return {
            value: this.students[index++],
            done: false
          }
        }else {
          return{
            value: undefined,
            done: true
          }
        }
      },
      return: () => {
        console.log('迭代器提前终止了')
        return {done: true, value: undefined}
      }
    }
  }
}
const classRoom = new ClassRoom('3幢5楼200', '计算机教室', ['james', 'curry', 'wts'])
classRoom.entry('lilei');

// for(const item of classroom){}  // 不是可迭代对象

// 逻辑是这样，但是有一个弊端，假如创建多个实例呢？又要写一遍吗
// classRoom[Symbol.iterator] = function () {
//   return {
//       next: function () {
          
//       }
//   }
// }

for(const item of classRoom) {
  console.log(item);
  if(item === 'wts') break;
}
```





## 自定义类的迭代实现

![image-20220711073955950](.\19_Iterator-Generator\image-20220711073955950.png)

![image-20220711074009661](.\19_Iterator-Generator\image-20220711074009661.png)





## 迭代器的中断

迭代器在某些情况下会在没有完全迭代的情况下中断：

- 比如遍历的过程中通过break、continue、return、throw中断了循环操作；
- 比如在解构的时候，没有解构所有的值；

那么这个时候我们想要监听中断的话，可以添加return方法：

![image-20220711074056080](.\19_Iterator-Generator\image-20220711074056080.png)

![image-20220711074106484](.\19_Iterator-Generator\image-20220711074106484.png)



## 什么是生成器？ 

生成器是ES6中新增的一种函数控制、使用的方案，它可以让我们更加灵活的控制函数什么时候继续执行、暂停执 行等。

生成器是跟函数有关系的，迭代器是跟对象有关系的

生成器函数也是一个函数，但是和普通的函数有一些区别：

- 首先，生成器函数需要在function的后面加一个符号：*
- 其次，生成器函数可以通过yield关键字来控制函数的执行流程：
- 最后，生成器函数的返回值是一个Generator（生成器）：
  - 生成器事实上是一种特殊的迭代器；
  - MDN：Instead, they return a special type of iterator, called a Generator.

![image-20220713072610081](.\19_Iterator-Generator\image-20220713072610081.png)

现在提一个要求在执行到第二行和第三行的时候，暂停一下，不要往后执行了

可以return，但是后面的代码永远执行不到了，但是要求是暂停，暂停之后是可以恢复的

这个时候就需要生成器来执行了

用生成器就可以精准的控制什么时候继续执行，什么时候暂停执行

生成器是和函数结合在一起的，生成器函数通常有一个结果，它返回的结果才叫生成器

```js
function* foo() {
  
}
```

![image-20220713073615226](.\19_Iterator-Generator\image-20220713073615226.png)

调用生成器函数时，会给我们返回一个生成器对象

![image-20220713073709508](.\19_Iterator-Generator\image-20220713073709508.png)

想让代码执行，该怎么做呢

![image-20220713074004092](.\19_Iterator-Generator\image-20220713074004092.png)

![image-20220713074031405](.\19_Iterator-Generator\image-20220713074031405.png)

```js
function* foo() {
  console.log('函数开始执行')
  const value1 = 100;
  console.log(value1);
  yield;

  const value2 = 200;
  console.log(value2);
  yield;

  const value3 = 300;
  console.log(value3)
  yield;

  console.log('函数执行结束')
}
const generator = foo();

// 开始执行第一段代码
generator.next();
generator.next();
generator.next();
generator.next();
generator.next();
```





## 生成器函数执行

我们发现上面的生成器函数foo的执行体压根没有执行，它只是返回了一个生成器对象。

- 那么我们如何可以让它执行函数中的东西呢？调用next即可；
- 我们之前学习迭代器时，知道迭代器的next是会有返回值的；
- 但是我们很多时候不希望next返回的是一个undefined，这个时候我们可以通过yield来返回结果；

![image-20220711074303792](.\19_Iterator-Generator\image-20220711074303792.png)



![image-20220711074317080](.\19_Iterator-Generator\image-20220711074317080.png)



迭代器在前面讲的时候我们说过它的next是有返回值的， 返回的是一个对象 {done: true, value: undefiend} 这种对象

那么生成器有没有返回值呢

```js
console.log(generator.next())
```

![image-20220713074404564](.\19_Iterator-Generator\image-20220713074404564.png)

![image-20220713074539105](.\19_Iterator-Generator\image-20220713074539105.png)

可以发现他就是一个迭代器，因为返回的格式和迭代器的格式是一样的，只不过它的value是一个undefined

![image-20220713074657513](.\19_Iterator-Generator\image-20220713074657513.png)

最后一次没有yield了，也就是说，相当于最后一次以后，相当于这个函数写了 return undefined了

那能不能有返回值呢

比如

![image-20220713074908553](.\19_Iterator-Generator\image-20220713074908553.png)

如果有返回值，就会将这个返回值作为value了



当我们的生成器函数，如果遇到yield，它会暂停函数的执行

如果遇到了return，它会整个函数停掉了，并且调用的next函数返回的done变成true，下面的所有的yield都不会执行了

![image-20220713075210199](.\19_Iterator-Generator\image-20220713075210199.png)

![image-20220714063911899](.\19_Iterator-Generator\image-20220714063911899.png)

这里的 yield 也可以是一个表达式，比如 yield value1 * 10

我们可以通过

```js
generator.next()
```

这种方式拿到yield的返回值

![image-20220714065121070](.\19_Iterator-Generator\image-20220714065121070.png)



第二段代码，应该是第二个next的时候







## 生成器传递参数 – next函数

函数既然可以暂停来分段执行，那么函数应该是可以传递参数的，我们是否可以给每个分段来传递参数呢？

- 答案是可以的；
- 我们在调用next函数的时候，可以给它传递参数，那么这个参数会作为上一个yield语句的返回值；
- 注意：也就是说我们是为本次的函数代码块执行提供了一个值；

![image-20220711074411111](.\19_Iterator-Generator\image-20220711074411111.png)



第二个yield这样传参

![image-20220714065517255](.\19_Iterator-Generator\image-20220714065517255.png)

```js
function* foo() {
  console.log('函数开始执行')
  const value1 = 100;
  console.log(value1);
  const n = yield value1;

  const value2 = 200 * n;
  console.log(value2, n);
  const y = yield;

  const value3 = 300;
  console.log(value3, y)
  const z = yield;

  console.log('函数执行结束', z)
  return '123'
}
const generator = foo();

// 开始执行第一段代码
console.log(generator.next());
console.log(generator.next(10));  // 这个传给的是第二段代码的yield,也就是n，但是从第一段yield的返回值拿到
console.log(generator.next(20));  // 这个传给的是第三段代码的yield,也就是n，但是从第二段yield的返回值拿到
console.log(generator.next(25));  // 这个传给的是第四段代码的yield,也就是n，但是从第三段yield的返回值拿到
```



那么第一段代码怎么传呢？

首先第一段代码，一般很少给他传递参数

首先我们在调用函数的时候可以给他传入参数

![image-20220714070208376](.\19_Iterator-Generator\image-20220714070208376.png)







## 生成器提前结束 – return函数

还有一个可以给生成器函数传递参数的方法是通过return函数：

- return传值后这个生成器函数就会结束，之后调用next不会继续生成值了；

![image-20220711074834662](.\19_Iterator-Generator\image-20220711074834662.png)



```js

function* foo(num) {
  const value1 = 100 * num
  console.log('第一段代码执行', value1);
  const n = yield value1;
  
  // return是在第一个next之后调用的，所以相当于是在这里加了return
  // 这个n是return传的参数，实际上和next传参一样，也是从上面一个yield的返回值
  // 中拿到这个n的
  // return n

  const value2 = 200 * n;
  console.log('第二段代码执行', value2)
  const count = yield value2;
}

const generator = foo(10);
console.log(generator.next());	// { value: 1000, done: false }
console.log(generator.return(30))	// { value: 30, done: true }
// 并且以后的代码都会终止掉
console.log(generator.next()); // {value: undefined, done: true}
```







## 生成器抛出异常 – throw函数

除了给生成器函数内部传递参数之外，也可以给生成器函数内部抛出异常：

- 抛出异常后我们可以在生成器函数中捕获异常；
- 但是在catch语句中不能继续yield新的值了，但是可以在catch语句外使用yield继续中断函数的执行；

![image-20220711074912766](.\19_Iterator-Generator\image-20220711074912766.png)

![image-20220711074922038](.\19_Iterator-Generator\image-20220711074922038.png)

![image-20220714071805745](.\19_Iterator-Generator\image-20220714071805745.png)

既然有异常，那么我们肯定也是可以捕获异常的

![image-20220714071951503](.\19_Iterator-Generator\image-20220714071951503.png)

当然throw也是可以传递信息，打印抛出的异常信息

![image-20220714072118608](.\19_Iterator-Generator\image-20220714072118608.png)

也可以这样



throw的应用场景

![image-20220714072245631](.\19_Iterator-Generator\image-20220714072245631.png)

假如上面的结果我不满意，那么我可以终止掉这段代码





## 生成器替代迭代器

我们发现生成器是一种特殊的迭代器，那么在某些情况下我们可以使用生成器来替代迭代器：

首先我们自己创建的迭代器

![image-20220725081607691](.\19_Iterator-Generator\image-20220725081607691.png)

用生成器来替代迭代器

![image-20220725081658786](.\19_Iterator-Generator\image-20220725081658786.png)

我们的目的是每调一次next就返回一个下面这样的对象

那么我们能不能让这个函数返回一个生成器呢？

因为生成器返回的对象可以调next

![image-20220725081920684](.\19_Iterator-Generator\image-20220725081920684.png)



哪怕是这样也可以

![image-20220725082108466](.\19_Iterator-Generator\image-20220725082108466.png)



不过最好这样

![image-20220711074949285](.\19_Iterator-Generator\image-20220711074949285.png)





事实上我们还可以使用yield*来生产一个可迭代对象：

- 这个时候相当于是一种yield的语法糖，只不过会依次迭代这个可迭代对象，每次迭代其中的一个值；

![image-20220711075032337](.\19_Iterator-Generator\image-20220711075032337.png)



![image-20220725082320246](.\19_Iterator-Generator\image-20220725082320246.png)

![image-20220725082427817](.\19_Iterator-Generator\image-20220725082427817.png)



如果有一个需求，让我们创建一个函数，这个函数可以迭代一个范围内的数字

![image-20220711075002480](.\19_Iterator-Generator\image-20220711075002480.png)





## 自定义类迭代 – 生成器实现

我们原来是这样写的

![image-20220725083130695](.\19_Iterator-Generator\image-20220725083130695.png)



class中的函数还可以这样写，发现可以调用的，所以，在class类中定义方法还有这种方式

![image-20220725083442352](.\19_Iterator-Generator\image-20220725083442352.png)

这样也可以

![image-20220725083831217](.\19_Iterator-Generator\image-20220725083831217.png)

所以symbol可以这样写

![image-20220725083903581](.\19_Iterator-Generator\image-20220725083903581.png)





在之前的自定义类迭代中，我们也可以换成生成器：

![image-20220711075101338](.\19_Iterator-Generator\image-20220711075101338.png)



迭代的是它

![image-20220725084029446](.\19_Iterator-Generator\image-20220725084029446.png)

这里迭代的是class类实例





## 对生成器的操作

既然生成器是一个迭代器，那么我们可以对其进行如下的操作：

![image-20220711075136259](.\19_Iterator-Generator\image-20220711075136259.png)





## 异步处理方案

学完了我们前面的Promise、生成器等，我们目前来看一下异步代码的最终处理方案。

这个是我们以前处理异步的方案

![image-20220725084918321](.\19_Iterator-Generator\image-20220725084918321.png)

又比如，你给我一个url，2秒后给你一个什么东西

![image-20220725085004379](.\19_Iterator-Generator\image-20220725085004379.png)





需求：

- 我们需要向服务器发送网络请求获取数据，一共需要发送三次请求；
- 第二次的请求url依赖于第一次的结果；
- 第三次的请求url依赖于第二次的结果；
- 依次类推；

例如这种

![image-20220725085115089](.\19_Iterator-Generator\image-20220725085115089.png)



![image-20220711075237143](.\19_Iterator-Generator\image-20220711075237143.png)



如果按照上面那种需求的话，我们可能要这样写，这种也被称为回调地狱

第一种方案

![image-20220711075250749](.\19_Iterator-Generator\image-20220711075250749.png)



第二种方案

![image-20220711075302953](.\19_Iterator-Generator\image-20220711075302953.png)

前面讲过，这里return函数实际是一个promise，并且then里面如果有then，那么里面的then会替代外层的then



第三种方案

## Generator方案

但是上面的代码其实看起来也是阅读性比较差的，有没有办法可以继续来对上面的代码进行优化呢？

![image-20220711075327704](.\19_Iterator-Generator\image-20220711075327704.png)

![image-20220711075339021](.\19_Iterator-Generator\image-20220711075339021.png)



![image-20220725092128179](.\19_Iterator-Generator\image-20220725092128179.png)

但是在这里拿到结果之后我们不要在这里用，我们还要传到这里来

![image-20220725092300576](.\19_Iterator-Generator\image-20220725092300576.png)

所以我们要执行下一次的next，并且给他传一个参数，那么这个参数就可以传到上面去了

![image-20220725092342712](.\19_Iterator-Generator\image-20220725092342712.png)

传过去之后，next执行完了，也就之下了下一个yield

![image-20220725092800081](.\19_Iterator-Generator\image-20220725092800081.png)

这样返回的就是第二个yield返回的结果也就是我们后面的value.then拿到的

这样拿到了res，但是我们依然不要用它，依然把他传出去

![image-20220725093224400](.\19_Iterator-Generator\image-20220725093224400.png)

但是可以发现下面还是有嵌套，但是他们是有规律的，既然有规律，那么我们可以写成一个递归函数





## 自动执行generator函数

目前我们的写法有两个问题：

- 第一，我们不能确定到底需要调用几层的Promise关系；
- 第二，如果还有其他需要这样执行的函数，我们应该如何操作呢？

所以，我们可以封装一个工具函数execGenerator自动执行生成器函数：

![image-20220711075432481](.\19_Iterator-Generator\image-20220711075432481.png)





```js

// 通过自动化函数，获取最后的yield结果
function execGenerator(genFn) {
  const generator = genFn()
  function exec(res) {
    const result = generator.next(res)
    if (result.done) {
      return result.value
    }
    result.value.then(res => {
      exec(res)
    })
  }
  exec()
}

execGenerator(getData)

// 每一次执行，都新增一些参数
function* getData() {
  const res1 = yield requestData("why")
  const res2 = yield requestData(res1 + "aaa")
  const res3 = yield requestData(res2 + "bbb")
  const res4 = yield requestData(res3 + "ccc")
  console.log(res4)
}

// request.js
function requestData(url) {
  // 异步请求的代码会被放入到executor中
  return new Promise((resolve, reject) => {
    // 模拟网络请求
    setTimeout(() => {
      // 拿到请求的结果
      resolve(url)
    }, 2000);
  })
}
```



这种代码的阅读性很强



方式四：

上面这个函数，可能我们自己写不出来，但是npm实际已经有这种包了

叫做 co

```js
// 每一次执行，都新增一些参数
function* getData() {
  const res1 = yield requestData("why")
  const res2 = yield requestData(res1 + "aaa")
  const res3 = yield requestData(res2 + "bbb")
  const res4 = yield requestData(res3 + "ccc")
  console.log(res4)
}

// request.js
function requestData(url) {
  // 异步请求的代码会被放入到executor中
  return new Promise((resolve, reject) => {
    // 模拟网络请求
    setTimeout(() => {
      // 拿到请求的结果
      resolve(url)
    }, 2000);
  })
}

npm install co

const co = require('co');
co(getData);
```



所以社区里早就有了这个函数了

这个包就是TJ写的



第五种方案

![image-20220725103853060](.\19_Iterator-Generator\image-20220725103853060.png)



async await的本质就是上面的生成器函数，它们是上面的一个语法糖

