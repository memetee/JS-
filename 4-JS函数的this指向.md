## 为什么需要this？

在常见的编程语言中，几乎都有this这个关键字（Objective-C中使用的是self），但是JavaScript中的this和常见的面向对象语 言中的this不太一样：

- 常见面向对象的编程语言中，比如Java、C++、Swift、Dart等等一系列语言中，this通常只会出现在类的方法中。
- 也就是你需要有一个类，类中的方法（特别是实例方法）中，this代表的是当前调用对象。但是JavaScript中的this更加灵活，无论是它出现的位置还是它代表的含义。

我们来看一下编写一个obj的对象，有this和没有this的区别

![image-20220705075612835](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705075612835.png)

直接用obj也是没有问题的，从某些角度来说，开发中没有this也是有解决方案的

但是如果上面的obj编程info了呢？那么obj.name就需要改成info.name,

所以，如果没有this，会让我们编写代码非常的不方便

![image-20220705075635550](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705075635550.png)



## this指向什么呢？

我们先说一个最简单的，this在全局作用于下指向什么？

浏览器：window(globalObject)

- 这个问题非常容易回答，在浏览器中测试就是指向window

![image-20220705075713152](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705075713152.png)



但是，开发中很少直接在全局作用于下去使用this，通常都是在函数中使用。

- 所有的函数在被调用时，都会创建一个执行上下文：
- 这个上下文中记录着函数的调用栈、AO对象等；
- this也是其中的一条记录；

node:{}(空对象)

![image-20220705075749507](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705075749507.png)



node中每一个js文件都是一个模块，首先会进行加载，然后编辑，然后他会放到一个函数中，并且调用这个函数，但是在调用这个函数的时候，他会改变this执行，通过apply的方式来调用，并且给这个apply传入一个空对象，所以，node中全局的this指向的是{}



node中为什么是{}

![image-20220705075812233](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705075812233.png)





## this到底指向什么呢？

this在内存中的哪个位置呢

globalObject

![image-20220705075840604](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705075840604.png)

我们先来看一个让人困惑的问题：

- 定义一个函数，我们采用三种不同的方式对它进行调用，它产生了三种不同的结果

![image-20220705075858476](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705075858476.png)



这个的案例可以给我们什么样的启示呢？

- 函数在调用时，JavaScript会默认给this绑定一个值；
- this的绑定和定义的位置（编写的位置）没有关系；
- this的绑定和调用方式以及调用的位置有关系；
- this是在运行时被绑定的；

那么this到底是怎么样的绑定规则呢？一起来学习一下吧

- 绑定一：默认绑定；
- 绑定二：隐式绑定；
- 绑定三：显示绑定；
- 绑定四：new绑定；





## 规则一：默认绑定

什么情况下使用默认绑定呢？独立函数调用.

- 独立的函数调用我们可以理解成函数没有被绑定到某个对象上进行调用；

我们通过几个案例来看一下，常见的默认绑定

![image-20220705080049774](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705080049774.png)

![image-20220705080101980](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705080101980.png)

![image-20220705080106904](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705080106904.png)

![image-20220705080111256](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705080111256.png)

闭包中的this

![image-20220705080128235](D:\studyMaterial\JS高级\笔记\4-JS函数的this指向\image-20220705080128235.png)

它们的打印都是window







