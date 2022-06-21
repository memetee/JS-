## 实现apply、call、bind

![image-20220617074755024](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617074755024.png)



当使用call/apply/bind的时候实际上，会在调用栈中绑定一个this，这个this的地址指向的就是传进来的对象



### 剩余参数

![image-20220617074815530](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617074815530.png)



### 展开运算符

![image-20220617074826906](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617074826906.png)



### 接下来我们来实现一下apply、call、bind函数：

- 注意：我们的实现是练习函数、this、调用关系，不会过度考虑一些边界情况

![image-20220617074846765](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617074846765.png)

![image-20220617074851606](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617074851606.png)



```javascript
// 2、这里是构造函数Function，给他的原型增加方法，那么其他的地方都可以调用了
// 接收传过来的参数, 第一个参数是this，第二个参数是剩余参数，也就是需要传进来的参数
Function.prototype.tsCall = function (thisArg, ...arg) {
  // 3、问题1：得获取到哪一个函数调用了tsCall
  // 4、既然要手动实现call方法，就需要在这个函数里面调用调用这个方法的函数
  // 5、foo()  不能写死，不具备通用性
  // 6、这个this指向的就是调用tsCall的那个函数，这里是通过隐式绑定来确定这个this绑定的是谁的
  var fn = this;


  // 7、这样不就白实现call方法了吗，肯定不能这样做啦
  // fn.call(thisArg);


  // 8、这样就可以拿到调用tsCall这个函数，直接调用
  // fn();
  // 9、问题2：如果是数字类型，是不能添加属性的，下面这样操作,这样不管是数字还是字符串都是对象类型了
  //10、 如果有值就通过Object函数来处理，如果没有值就是window
  // 11、他的意思是把传过来的这个this也就是需要绑定的对象拿到，拿到以后转换成对象类型，如果是null或者是undefined则绑定window;
  thisArg = thisArg ? Object(thisArg) : window;
  // 12、给需要绑定的这个对象增加一个属性，这个属性就是调用tsCall的函数，为什么要加给他呢，这里是做隐式绑定
  // 这个时候fn里面的this指向的就是传进来的那个this了
  thisArg.fn = fn;


  // 13、这样调用的话，在调用的这个函数里面实际上会多出来一个属性，就是fn这个属性，
  // 是因为我们在上面给thisArg增加了这个属性，现在调用的话实际上这个属性是没有删除
  // 的，如果不想要这个属性我们可以在后面通过delete删除这个属性
  // 14、拿到除了thisArg之外剩余的参数，传给调用tsCall的函数
  var result = thisArg.fn(...arg);


  // 14、将这个函数的返回值也返回回去
  return result;


}


// 1、调用，并看下这个this是什么
function foo() {
  console.log('foo函数被执行了', this)
}


// 2、带返回值的情况
function sum(num1, num2) {
  return num1 + num2;
}


// 系统的call方法传入数字类型，或者字符串类型，是没有问题的
// foo.call(123);
// 这里是在隐式绑定this,所以在tsCall中的this就是foo函数
// foo.tsCall({name: 'wts'});


// 我们实现的call如果传入数字类型或者字符串就会有问题了
// 为什么会有问题呢，因为数字上面是不能加东西的，比如 123.fn这个是会报错的
// 如果想加怎么办呢，就需要 var num = new Number(123); 把他变成一个Number类型
// 但是你怎么知道我们传的是什么类型呢，有可能是number类型，有可能是boolean类型，也有可能是string类型
// 我们可以这样 Object(123)、Object('hehe');
// 这样的话就可以得到对象，比如Number类型、或者String类型
foo.tsCall(null)
console.log(sum.tsCall({}, 3, 45))
// sum.tsCall();
```



![image-20220617074914667](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617074914667.png)



```javascript
// 自己实现hyapply
Function.prototype.hyapply = function(thisArg, argArray) {
  // 1.获取到要执行的函数
  var fn = this


  // 2.处理绑定的thisArg
  thisArg = (thisArg !== null && thisArg !== undefined) ? Object(thisArg): window


  // 3.执行函数
  thisArg.fn = fn
  var result
  // if (!argArray) { // argArray是没有值(没有传参数)
  //   result = thisArg.fn()
  // } else { // 有传参数
  //   result = thisArg.fn(...argArray)
  // }


  // argArray = argArray ? argArray: []
  argArray = argArray || []
  result = thisArg.fn(...argArray)


  delete thisArg.fn


  // 4.返回结果
  return result
}


function sum(num1, num2) {
  console.log("sum被调用", this, num1, num2)
  return num1 + num2
}


function foo(num) {
  return num
}


function bar() {
  console.log("bar函数被执行", this)
}


// 系统调用
// var result = sum.apply("abc", 20)
// console.log(result)


// 自己实现的调用
// var result = sum.hyapply("abc", [20, 30])
// console.log(result)


// var result2 = foo.hyapply("abc", [20])
// console.log(result2)


// edge case
bar.hyapply(0)
```



### bind实现

```javascript
Function.prototype.hybind = function(thisArg, ...argArray) {
  // 1.获取到真实需要调用的函数
  var fn = this


  // 2.绑定this
  thisArg = (thisArg !== null && thisArg !== undefined) ? Object(thisArg): window


  function proxyFn(...args) {
    // 3.将函数放到thisArg中进行调用
    thisArg.fn = fn
    // 特殊: 对两个传入的参数进行合并
    var finalArgs = [...argArray, ...args]
    var result = thisArg.fn(...finalArgs)
    delete thisArg.fn


    // 4.返回结果
    return result
  }


  return proxyFn
}


function foo() {
  console.log("foo被执行", this)
  return 20
}


function sum(num1, num2, num3, num4) {
  console.log(num1, num2, num3, num4)
}


// 系统的bind使用
var bar = foo.bind("abc")
bar()


// var newSum = sum.bind("aaa", 10, 20, 30, 40)
// newSum()


// var newSum = sum.bind("aaa")
// newSum(10, 20, 30, 40)


// var newSum = sum.bind("aaa", 10)
// newSum(20, 30, 40)




// 使用自己定义的bind
// var bar = foo.hybind("abc")
// var result = bar()
// console.log(result)


var newSum = sum.hybind("abc", 10, 20)
var result = newSum(30, 40)
```









## 认识arguments

arguments 是一个 对应于 传递给函数的参数 的 类数组(array-like)对象。

![image-20220617075027631](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075027631.png)



array-like意味着它不是一个数组类型，而是一个对象类型：

- 但是它却拥有数组的一些特性，比如说length，比如可以通过index索引来访问；
- 但是它却没有数组的一些方法，比如forEach、map等；

![image-20220617075051133](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075051133.png)



arguments实际上也是在AO里面的一个变量 

```javascript
var ao = {
	name: undefined,
	age: undefined
	arguments: {}   //他实际是一个对象
}
```

arguments.callee这个属性获取的是当前arguments所在的函数，如果使用arguments.callee()来调用会引起递归的问题注意：arguments可以通过for循环来遍历，但是不能通过forEach或者map来遍历





## arguments转成array

![image-20220617075138110](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075138110.png)



## slice的原理

```javascript
// 额外补充的知识点: Array中的slice实现
//slice的原理就是拿到传过来的数据，以及需要拿到的索引，然后遍历这个索引，拿到每一项的值，放到一个新的数组里面，并且将这个数组进行返回
Array.prototype.hyslice = function(start, end) {
  var arr = this
  start = start || 0
  end = end || arr.length
  var newArray = []
  for (var i = start; i < end; i++) {
    newArray.push(arr[i])
  }
  return newArray
}


var newArray = Array.prototype.hyslice.call(["aaaa", "bbb", "cccc"], 1, 3)
console.log(newArray)


var names = ["aaa", "bbb", "ccc", "ddd"]
names.slice(1, 3)

      // 这两种写法是一样的
      var arr = Array.prototype.slice.call(arguments);
      var arr = [].slice.call(arguments)
```

所以上面的arguments转成数组的第二种方法，就是利用slice的特点，来遍历arguments，然后生成一个新的array。用call的原因是arguments不是一个array，不能使用slice方法，所以我们借用了array的slice方法，但是改变它的this指向，让他来遍历argument，生成的新的数组就是我们需要的数组







## 箭头函数不绑定arguments

箭头函数是不绑定arguments的，所以我们在箭头函数中使用arguments会去上层作用域查找：

![image-20220617075228759](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075228759.png)

![image-20220617075233192](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075233192.png)



箭头函数中的arguments在浏览器中是没有的

![image-20220617075243783](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075243783.png)



箭头函数在nodejs中的arguments是存在的

![image-20220617075256435](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075256435.png)



为什么在node中会有这些东西呢，是因为每一个js文件在node中都是一个模块，最后这个模块会被一个函数包裹住

```javascript
//拿到那个包裹的函数执行call
.call({}, 参数一， 参数二， 参数三)
```



然后调用这个函数，这些参数都是arguments打印出来的东西

![image-20220617075321419](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075321419.png)



箭头函数没有arguments，那么如果想在箭头函数中拿到参数，该怎么办呢？

使用剩余参数

![image-20220617075331539](D:\studyMaterial\JS高级\笔记\Untitled\image-20220617075331539.png)

它的语法就是...args









## 理解JavaScript纯函数

函数式编程中有一个非常重要的概念叫纯函数，JavaScript符合函数式编程的范式，所以也有纯函数的概念；

- 在react开发中纯函数是被多次提及的；
- 比如react中组件就被要求像是一个纯函数（为什么是像，因为还有class组件），redux中有一个reducer的概念，也 是要求必须是一个纯函数；
- 所以掌握纯函数对于理解很多框架的设计是非常有帮助的；

纯函数的维基百科定义：

- 在程序设计中，若一个函数符合以下条件，那么这个函数被称为纯函数：
- 此函数在相同的输入值时，需产生相同的输出。

```javascript
反例：

var name = 'abc'
function foo(number){
    return name
}
foo(123) 
name = 'wts';
foo(123)
这个就是相同的输入，产生了不同的输出，这个函数依赖了外部的变量，但是外部变量发生了改变；
所以他就不是一个纯函数
```

- 函数的输出和输入值以外的其他隐藏信息或状态无关，也和由I/O设备产生的外部输出（例如键盘点击）无关（只跟输入值也就是参数，和函数内部的执行有关，和外部变量无关）。
- 该函数不能有语义上可观察的函数副作用，诸如“触发事件”，使输出设备输出，或更改输出值以外物件的内容等（例如，触发事件的时候，这个函数不能执行 ）。



当然上面的定义会过于的晦涩，所以我简单总结一下：

- 确定的输入，一定会产生确定的输出；
- 函数在执行过程中，不能产生副作用；























