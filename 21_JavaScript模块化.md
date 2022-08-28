## 什么是模块化？

到底什么是模块化、模块化开发呢？

- 事实上模块化开发最终的目的是将程序划分成一个个小的结构；
- 这个结构中编写属于自己的逻辑代码，有自己的作用域，不会影响到其他的结构；
- 这个结构可以将自己希望暴露的变量、函数、对象等导出给其结构使用；
- 也可以通过某种方式，导入另外结构中的变量、函数、对象等；

上面说提到的结构，就是模块；按照这种结构划分开发程序的过程，就是模块化开发的过程；

无论你多么喜欢JavaScript，以及它现在发展的有多好，它都有很多的缺陷：

- 比如var定义的变量作用域问题；
- 比如JavaScript的面向对象并不能像常规面向对象语言一样使用class；
- 比如JavaScript没有模块化的问题；

Brendan Eich本人也多次承认过JavaScript设计之初的缺陷，但是随着JavaScript的发展以及标准化，存在的缺陷问题基 本都得到了完善。

无论是web、移动端、小程序端、服务器端、桌面应用都被广泛的使用；

![image-20220802073858454](.\21_JavaScript模块化\image-20220802073858454.png)

它们是没有独立空间的，也就是说在aaa.js里面定义的name在bbb可以访问的，那么在bbb定义了name它们就会冲突的

那么我们想做的其实是

![image-20220802074209772](.\21_JavaScript模块化\image-20220802074209772.png)

在某一个独立的空间中，你们是无法访问的，也就是我在a文件定义了sum，b文件是无法访问的，如果你想访问，那么可以通过在a文件抛出，在b文件引入的方式来获取

一般来说，一个独立的文件就是一个模块

这种开发模式就是模块化开发



## 模块化的历史

在网页开发的早期，Brendan Eich开发JavaScript仅仅作为一种脚本语言，做一些简单的表单验证或动画实现等，那个时 候代码还是很少的：

- 这个时候我们只需要讲JavaScript代码写到
- 并没有必要放到多个文件中来编写；甚至流行：通常来说 JavaScript 程序的长度只有一行。

但是随着前端和JavaScript的快速发展，JavaScript代码变得越来越复杂了：

- ajax的出现，前后端开发分离，意味着后端返回数据后，我们需要通过JavaScript进行前端页面的渲染；
- SPA的出现，前端页面变得更加复杂：包括前端路由、状态管理等等一系列复杂的需求需要通过JavaScript来实现；
- 包括Node的实现，JavaScript编写复杂的后端程序，没有模块化是致命的硬伤；

所以，模块化已经是JavaScript一个非常迫切的需求：

- 但是JavaScript本身，直到ES6（2015）才推出了自己的模块化方案；
- 在此之前，为了让JavaScript支持模块化，涌现出了很多不同的模块化规范：AMD、CMD、CommonJS等；

在我们的课程中，我将详细讲解JavaScript的模块化，尤其是CommonJS和ES6的模块化。









## 没有模块化带来的问题

早期没有模块化带来了很多的问题：比如命名冲突的问题



当然，我们有办法可以解决上面的问题：立即函数调用表达式（IIFE）

- IIFE (Immediately Invoked Function Expression)

变量这样定义

每个文件中的代码都写到函数中

文件1：

![image-20220802075811575](.\21_JavaScript模块化\image-20220802075811575.png)

这样使用

文件2：

![image-20220802075840632](.\21_JavaScript模块化\image-20220802075840632.png)



但是，我们其实带来了新的问题：

- 第一，我必须记得每一个模块中返回对象的命名，才能在其他模块使用过程中正确的使用；
- 第二，代码写起来混乱不堪，每个文件中的代码都需要包裹在一个匿名函数中来编写；
- 第三，在没有合适的规范情况下，每个人、每个公司都可能会任意命名、甚至出现模块名称相同的情况；

所以，我们会发现，虽然实现了模块化，但是我们的实现过于简单，并且是没有规范的。

- 我们需要制定一定的规范来约束每个人都按照这个规范去编写模块化的代码;
- 这个规范中应该包括核心功能：模块本身可以导出暴露的属性，模块又可以导入自己需要的属性；
- JavaScript社区为了解决上面的问题，涌现出一系列好用的规范，接下来我们就学习具有代表性的一些规范。



## CommonJS规范和Node关系

我们需要知道CommonJS是一个规范，最初提出来是在浏览器以外的地方使用，并且当时被命名为ServerJS，后来为了 体现它的广泛性，修改为CommonJS，平时我们也会简称为CJS。

- Node是CommonJS在服务器端一个具有代表性的实现；
- Browserify是CommonJS在浏览器中的一种实现；
- webpack打包工具具备对CommonJS的支持和转换；

所以，Node中对CommonJS进行了支持和实现，让我们在开发node的过程中可以方便的进行模块化开发：

- 在Node中每一个js文件都是一个单独的模块；
- 这个模块中包括CommonJS规范的核心变量：exports、module.exports、require；
- 我们可以使用这些变量来方便的进行模块化开发；

前面我们提到过模块化的核心是导出和导入，Node中对其进行了实现：

- exports和module.exports可以负责对模块中的内容进行导出；
- require函数可以帮助我们导入其他模块（自定义模块、系统模块、第三方库模块）中的内容；







## 模块化案例

我们来看一下两个文件：

![image-20220726084203574](.\21_JavaScript模块化\image-20220726084203574.png)











## exports导出

注意：exports是一个对象，我们可以在这个对象中添加很多个属性，添加的属性会导出；

![image-20220726084238321](.\21_JavaScript模块化\image-20220726084238321.png)

另外一个文件中可以导入：

![image-20220726084314025](.\21_JavaScript模块化\image-20220726084314025.png)

上面这行完成了什么操作呢？理解下面这句话，Node中的模块化一目了然

- 意味着main中的bar变量等于exports对象；
- 也就是require通过各种查找方式，最终找到了exports这个对象；
- 并且将这个exports对象赋值给了bar变量；
- bar变量就是exports对象了；







## module.exports

但是Node中我们经常导出东西的时候，又是通过module.exports导出的：

- module.exports和exports有什么关系或者区别呢？

我们追根溯源，通过维基百科中对CommonJS规范的解析：

- CommonJS中是没有module.exports的概念的；
- 但是为了实现模块的导出，Node中使用的是Module的类，每一个模块都是Module的一个实例，也就是 module；
- 所以在Node中真正用于导出的其实根本不是exports，而是module.exports；
- 因为module才是导出的真正实现者；

但是，为什么exports也可以导出呢？

- 这是因为module对象的exports属性是exports对象的一个引用；
- 也就是说 module.exports = exports = main中的bar；

![image-20220803074216746](.\21_JavaScript模块化\image-20220803074216746.png)

![image-20220803074540185](.\21_JavaScript模块化\image-20220803074540185.png)

![image-20220803075208042](.\21_JavaScript模块化\image-20220803075208042.png)

验证

![image-20220803075304935](.\21_JavaScript模块化\image-20220803075304935.png)

在导出的文件中，1秒钟把name改成kobe

![image-20220803075337108](.\21_JavaScript模块化\image-20220803075337108.png)

在导入的文件中，2秒钟后打印name

![image-20220803075407044](.\21_JavaScript模块化\image-20220803075407044.png)

可以发现，确实有改变，说明它们确实是同一个对象

我们还可以这样

![image-20220803075519474](.\21_JavaScript模块化\image-20220803075519474.png)

在导入的对象中，将name修改一下

在导出的文件中2秒钟后看一下name是否有改变

![image-20220803075559321](.\21_JavaScript模块化\image-20220803075559321.png)

依然有改变

它们就是那副图中的同一个对象，这个就是引用的原理。



我们可能见过exports导出，那么exports导出和module.exports导出有什么区别呢？

![image-20220804071143782](.\21_JavaScript模块化\image-20220804071143782.png)

源码实际上是这样做的，会把module.exports赋值给exports

所以他们是同一个对象，引用是相同的



要知道，node中导出的一定是module.exports并不是exports，之所以可以用exports，是因为上面module.exports有给exports做一个引用赋值，修改了exports中的引用，module.exports也会发生改变。但是如果exports重新赋值一个对象，那么就无法导出了

![image-20220804071639111](.\21_JavaScript模块化\image-20220804071639111.png)

如果exports脱离了module.exports，那么就无法导出了

![image-20220804071952440](.\21_JavaScript模块化\image-20220804071952440.png)



## 改变代码发生了什么?

我们这里从几个方面来研究修改代码发生了什么？

- 1.在三者项目引用的情况下（require, export, module.export），修改exports中的name属性到底发生了什么？
- 2.在三者引用的情况下，修改了main中的bar的name属性，在bar模块中会发生什么？
- 3.如果module.exports不再引用exports对象了，那么修改export还有意义吗？
  - 为了复合commonjs规范，加了一个exports变量，它的原理就是module.exports

先画出在内存中发生了什么，再得出最后的结论。

![image-20220804072156496](.\21_JavaScript模块化\image-20220804072156496.png)









## require细节

我们现在已经知道，require是一个函数，可以帮助我们引入一个文件（模块）中导出的对象。

那么，require的查找规则是怎么样的呢？

- https://nodejs.org/dist/latest-v14.x/docs/api/modules.html

这里我总结比较常见的查找规则：导入格式如下：require(X)

**情况一：X是一个Node核心模块，比如path、http**

- 直接返回核心模块，并且停止查找



**情况二：X是以 ./ 或 ../ 或 /（根目录）开头的**

第一步：将X当做一个文件在对应的目录下查找；

- 1.如果有后缀名，按照后缀名的格式查找对应的文件
- 2.如果没有后缀名，会按照如下顺序：
  - 1> 直接查找文件X
  - 2> 查找X.js文件
  - 3> 查找X.json文件
  - 4> 查找X.node文件
  

第二步：没有找到对应的文件，将X作为一个目录

- 查找目录下面的index文件
  - 1> 查找X/index.js文件
  - 2> 查找X/index.json文件
  - 3> 查找X/index.node文件

  如果没有找到，那么报错：not found

  

**情况三：直接是一个X（没有路径），并且X不是一个核心模块**

/Users/coderwhy/Desktop/Node/TestCode/04_learn_node/05_javascript-module/02_commonjs/main.js中编写 require('why’)

![image-20220726084849314](.\21_JavaScript模块化\image-20220726084849314.png)

如果上面的路径中都没有找到，那么报错：not found

所以假设我们下载了一个axios，它的查找规则就是在我们当前的node_modules中去查找的

![image-20220804074230704](.\21_JavaScript模块化\image-20220804074230704.png)

找到之后，会自动给我们拼接index.js，然后去里面找index.js文件的

如果我们全局安装，因为它是一层一层的往上查找的，所以也是可以找到的



## 模块的加载过程

结论一：模块在被第一次引入时，模块中的js代码会被运行一次

![image-20220805071131398](.\21_JavaScript模块化\image-20220805071131398.png)



结论二：模块被多次引入时，会缓存，最终只加载（运行）一次

- 为什么只会加载运行一次呢？
- 这是因为每个模块对象module都有一个属性：loaded。
- 为false表示还没有加载，为true表示已经加载；

结论三：如果有循环引入，那么加载顺序是什么？



如果出现右图模块的引用关系，那么加载顺序是什么呢？

- 这个其实是一种数据结构：图结构；
- 图结构在遍历的过程中，有深度优先搜索（DFS, depth first search）和广度优先搜索（BFS, breadth first search）；
- Node采用的是深度优先算法：main -> aaa -> ccc -> ddd -> eee ->bbb

![image-20220726085021389](.\21_JavaScript模块化\image-20220726085021389.png)





## CommonJS规范缺点

CommonJS规范缺点

- 同步的意味着只有等到对应的模块加载完毕，当前模块中的内容才能被运行；
- 这个在服务器不会有什么问题，因为服务器加载的js文件都是本地文件，加载速度非常快；

如果将它应用于浏览器呢？

- 浏览器加载js文件需要先从服务器将文件下载下来，之后再加载运行；
- 那么采用同步的就意味着后续的js代码都无法正常运行，即使是一些简单的DOM操作；

所以在浏览器中，我们通常不使用CommonJS规范：

- 当然在webpack中使用CommonJS是另外一回事；
- 因为它会将我们的代码转成浏览器可以直接执行的代码；

在早期为了可以在浏览器中使用模块化，通常会采用AMD或CMD：

- 但是目前一方面现代的浏览器已经支持ES Modules，另一方面借助于webpack等工具可以实现对CommonJS或者ES  Module代码的转换；
- AMD和CMD已经使用非常少了，所以这里我们进行简单的演练；





## AMD规范

AMD主要是应用于浏览器的一种模块化规范：

- AMD是Asynchronous Module Definition（异步模块定义）的缩写；
- 它采用的是异步加载模块；
- 事实上AMD的规范还要早于CommonJS，但是CommonJS目前依然在被使用，而AMD使用的较少了；

我们提到过，规范只是定义代码的应该如何去编写，只有有了具体的实现才能被应用：

- AMD实现的比较常用的库是require.js和curl.js；







## require.js的使用

第一步：下载require.js

- 下载地址：https://github.com/requirejs/requirejs
- 找到其中的require.js文件；

第二步：定义HTML的script标签引入require.js和定义入口文件：

- data-main属性的作用是在加载完src的文件后会加载执行该文件

![image-20220805073527916](.\21_JavaScript模块化\image-20220805073527916.png)

不能直接上面这样引用，要在下面这样引用

```js
<script src="./lib/require.js" data-main="./index.js"></script>
// 当加载了require之后，再加载index.js
```

![image-20220805073943851](.\21_JavaScript模块化\image-20220805073943851.png)

我们在文件中需要在define中写了

![image-20220805074111387](.\21_JavaScript模块化\image-20220805074111387.png)

![image-20220805074221981](.\21_JavaScript模块化\image-20220805074221981.png)

注意这里不需要src

上面的案例说明了一个问题，在foo中导出了东西，在main.js导入了东西，所以，这个就实现了模块化了

![image-20220805074407664](.\21_JavaScript模块化\image-20220805074407664.png)

如果给baseUrl为空

那么就需要给foo加src

![image-20220805074622336](.\21_JavaScript模块化\image-20220805074622336.png)

然后我们在bar模块中引入foo

![image-20220805074648040](.\21_JavaScript模块化\image-20220805074648040.png)

![image-20220805074758539](.\21_JavaScript模块化\image-20220805074758539.png)



![image-20220726130521419](.\21_JavaScript模块化\image-20220726130521419.png)

![image-20220726130535618](.\21_JavaScript模块化\image-20220726130535618.png)

![image-20220726130549285](.\21_JavaScript模块化\image-20220726130549285.png)





## CMD规范

CMD规范也是应用于浏览器的一种模块化规范：

- CMD 是Common Module Definition（通用模块定义）的缩写；
- 它也采用了异步加载模块，但是它将CommonJS的优点吸收了过来；
- 但是目前CMD使用也非常少了；

CMD也有自己比较优秀的实现方案：

- SeaJS

它们都是用在浏览器上的，所以要有html文件





## SeaJS的使用

第一步：下载SeaJS

- 下载地址：https://github.com/seajs/seajs
- 找到dist文件夹下的sea.js

第二步：引入sea.js和使用主入口文件

- seajs是指定主入口文件的

![image-20220805075117151](.\21_JavaScript模块化\image-20220805075117151.png)

导入

![image-20220805075437264](.\21_JavaScript模块化\image-20220805075437264.png)

导出

![image-20220805075600618](.\21_JavaScript模块化\image-20220805075600618.png)





![image-20220726130732078](.\21_JavaScript模块化\image-20220726130732078.png)

![image-20220726130741886](.\21_JavaScript模块化\image-20220726130741886.png)

![image-20220726130756344](.\21_JavaScript模块化\image-20220726130756344.png)

![image-20220726130814639](.\21_JavaScript模块化\image-20220726130814639.png)







## 认识 ES Module

JavaScript没有模块化一直是它的痛点，所以才会产生我们前面学习的社区规范：CommonJS、AMD、CMD等， 所以在ES推出自己的模块化系统时，大家也是兴奋异常。

ES Module和CommonJS的模块化有一些不同之处：

- 一方面它使用了import和export关键字；
- 另一方面它采用编译期的静态分析，并且也加入了动态引用的方式；

ES Module模块采用export和import关键字来实现模块化：

- export负责将模块内的内容导出；
- import负责从其他模块导入内容；

了解：采用ES Module将自动采用严格模式：use strict

- 如果你不熟悉严格模式可以简单看一下MDN上的解析；
- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode

![image-20220811073620917](.\21_JavaScript模块化\image-20220811073620917.png)

![image-20220811071338172](.\21_JavaScript模块化\image-20220811071338172.png)

![image-20220811071454616](.\21_JavaScript模块化\image-20220811071454616.png)‘![image-20220811071656889](.\21_JavaScript模块化\image-20220811071656889.png)

并没有给他当成模块化

所以必须要这样

![image-20220811071734587](.\21_JavaScript模块化\image-20220811071734587.png)

这样就可以了



live-server

![image-20220811072026171](.\21_JavaScript模块化\image-20220811072026171.png)

如果通过本地打开也会报错

![image-20220811072121747](.\21_JavaScript模块化\image-20220811072121747.png)

是因为当前，如果要把一个文件当成一个模块

![image-20220811072221371](.\21_JavaScript模块化\image-20220811072221371.png)

你不能用file这个url-scheme前缀，你可以用http、或者https

![image-20220811072408043](.\21_JavaScript模块化\image-20220811072408043.png)

file不能正常的去加载一个模块

![image-20220811072541661](.\21_JavaScript模块化\image-20220811072541661.png)

![image-20220811072747912](.\21_JavaScript模块化\image-20220811072747912.png)

这样才能正确解析

这就是模块化的基本使用





## 案例代码结构组件

这里我在浏览器中演示ES6的模块化开发：

```js
<script src="./modules/foo.js" type="module"></script>
<script src="main.js" type="module"></script>

```





如果直接在浏览器中运行代码，会报如下错误：

![image-20220726131017706](.\21_JavaScript模块化\image-20220726131017706.png)



这个在MDN上面有给出解释：

- https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules
- 你需要注意本地测试 — 如果你通过本地加载Html 文件 (比如一个 file:// 路径的文件), 你将会遇到 CORS 错误，因为 Javascript 模块安全性需要。
- 你需要通过一个服务器来测试。

我这里使用的VSCode，VSCode中有一个插件：Live Server







## exports关键字

export关键字将一个模块中的变量、函数、类等导出；

我们希望将其他中内容全部导出，它可以有如下的方式：

方式一：在语句声明的前面直接加上export关键字

方式二：将所有需要导出的标识符，放到export后面的 {}中

- 注意：这里的 {}里面不是ES6的对象字面量的增强写法，{}也不是表示一个对象的；
- 所以： export {name: name}，是错误的写法；

方式三：导出时给标识符起一个别名







## import关键字

import关键字负责从另外一个模块中导入内容

导入内容的方式也有多种：

方式一：import {标识符列表} from '模块'；

- 注意：这里的{}也不是一个对象，里面只是存放导入的标识符列表内容；

方式二：导入时给标识符起别名

方式三：通过 * 将模块功能放到一个模块功能对象（a module object）上

```js
// 第一种方式： export 声明语句，注意，这里不是exports
export const name = 'why';
export function foo() {
	console.log('foo function')
}
export class Person{

}

// 第二种方式：export 导出和声明分开
const name = 'why';
const age = 19;
function foo() {
    console.log('foo function')
}

export {
	name,
    age,
    foo
}
// 这是一个固定语法，它不是一个对象,他就是一个大括号把要导出的东西挨个写在里面
// 千万不能这样写
export {
	name: name
}

// 第三种方式： 第二种的变种，第二种导出时起别名
export {
	name as fName,
    age as fAge,
    foo as fFoo
}
// 导入的时候就要用新的名字了
import {fName, fAge, fFoo} from './foo.js'
// 一般不在导出的时候起别名
// 习惯用哪种导出就用哪种
```



导入

```js
// 导入方式一：普通的导入
import {name, age, foo} from './foo.js'
// 导入方式二：起别名
import {name as fName, age as fAge, foo as fFoo} from './foo.js';
// 导入方式三： 将导出的所有内容放到一个标识符中
import * as foo from './foo.js'	// 说明将所有的东西都放到foo中
foo.name;
foo.age;
// 这样也可以避免命名冲突
```









## export和import结合使用

补充：export和import可以结合使用

![image-20220726131239448](.\21_JavaScript模块化\image-20220726131239448.png)

为什么要这样做呢？

- 在开发和封装一个功能库时，通常我们希望将暴露的所有接口放到一个文件中；
- 这样方便指定统一的接口规范，也方便阅读；
- 这个时候，我们就可以使用export和import结合使用；

![image-20220811075232389](.\21_JavaScript模块化\image-20220811075232389.png)

使用的时候这样就可以了

![image-20220811075253183](.\21_JavaScript模块化\image-20220811075253183.png)



上面的导出有些繁琐，还有简单的办法

![image-20220811075433209](.\21_JavaScript模块化\image-20220811075433209.png)

还有第三种方法

![image-20220811075543461](.\21_JavaScript模块化\image-20220811075543461.png)

依然可以正常使用，导入的时候也是用上面的导入就行





## default用法

前面我们学习的导出功能都是有名字的导出（named exports）：

- 在导出export时指定了名字；
- 在导入import时需要知道具体的名字；

还有一种导出叫做默认导出（default export）

- 默认导出export时可以不需要指定名字；
- 在导入时不需要使用 {}，并且可以自己来指定名字；
- 它也方便我们和现有的CommonJS等规范相互操作；



注意：在一个模块中，只能有一个默认导出（default export）；

![image-20220811075945562](.\21_JavaScript模块化\image-20220811075945562.png)

![image-20220811080005351](.\21_JavaScript模块化\image-20220811080005351.png)

![image-20220811080048222](.\21_JavaScript模块化\image-20220811080048222.png)

但是下面的用法用的多一点

注意： 默认导出只能有一个





## import函数

通过import加载一个模块，是不可以在其放到逻辑代码中的，比如：

为什么会出现这个情况呢？

- 这是因为ES Module在被JS引擎解析时，就必须知道它的依赖关系；
- 由于这个时候js代码没有任何的运行，所以无法在进行类似于if判断中根据代码的执行情况；
- 甚至下面的这种写法也是错误的：因为我们必须到运行时能确定path的值；

但是某些情况下，我们确确实实希望动态的来加载某一个模块：

- 如果根据不懂的条件，动态来选择加载模块的路径；
- 这个时候我们需要使用 import() 函数来动态加载；

![image-20220726131506371](.\21_JavaScript模块化\image-20220726131506371.png)

![image-20220726131523445](.\21_JavaScript模块化\image-20220726131523445.png)



![image-20220811080432944](.\21_JavaScript模块化\image-20220811080432944.png)

必须要前面的代码解析完之后，才执行后面的代码

因为后面的代码如果用到前面的命名，前面的解析完才能变量才能使用

那我不想等你解析完，在执行后面的代码怎么办呢

![image-20220811080632960](.\21_JavaScript模块化\image-20220811080632960.png)

那怎么拿到结果呢

![image-20220811080702743](.\21_JavaScript模块化\image-20220811080702743.png)

![image-20220811080737544](.\21_JavaScript模块化\image-20220811080737544.png)

可以发现没有影响后面的代码运行

下载和解析不是由js线程来执行的，执行才是js线程做的

import 可以直接当成一个函数来执行

就是上面那样



import 也可以当成一个对象来使用

![image-20220811080933000](.\21_JavaScript模块化\image-20220811080933000.png)

就是一个下载的url

![image-20220811080912896](.\21_JavaScript模块化\image-20220811080912896.png)

下载的路径保存在meta属性中



## import meta

import.meta是一个给JavaScript模块暴露特定上下文的元数据属性的对象。

- 它包含了这个模块的信息，比如说这个模块的URL；
- 在ES11（ES2020）中新增的特性；









## ES Module的解析流程

ES Module是如何被浏览器解析并且让模块之间可以相互引用的呢？

- https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/

ES Module的解析过程可以划分为三个阶段：

- 阶段一：构建（Construction），根据地址查找js文件，并且下载，将其解析成模块记录（Module Record）；
- 阶段二：实例化（Instantiation），对模块记录进行实例化，并且分配内存空间，解析模块的导入和导出语句，把模块指向 对应的内存地址。
- 阶段三：运行（Evaluation），运行代码，计算值，并且将值填充到内存地址中；

![image-20220726132034128](.\21_JavaScript模块化\image-20220726132034128.png)



esmodule原理

浏览器如何对esmodule进行解析呢？

![image-20220812075142703](.\21_JavaScript模块化\image-20220812075142703.png)





## 阶段一：构建阶段

![image-20220726132057480](.\21_JavaScript模块化\image-20220726132057480.png)

![image-20220815070001706](.\21_JavaScript模块化\image-20220815070001706.png)

静态分析，不是运行代码

![image-20220815070213299](.\21_JavaScript模块化\image-20220815070213299.png)





这些js文件应该是在服务器中，应该先找到js文件，然后给他下载下来

下载下来之后，esmodule才能对它进行解析

在main的js中还会引用其他的js文件

![image-20220812075308956](.\21_JavaScript模块化\image-20220812075308956.png)

那么这些js文件应该会继续被下载，如果foo.js文件又有引用其他的文件，那么应该会继续被下载

应该是这样一个过程

![image-20220812075448018](.\21_JavaScript模块化\image-20220812075448018.png)

因为file是本地的，但是esmodule是需要下载的，必须要通过http/https进行下载的，必须有这样一个过程



那么下载下来之后会怎么做呢？

![image-20220812075818709](.\21_JavaScript模块化\image-20220812075818709.png)

这个就是构建



## 阶段二和三：实例化阶段 – 求值阶段

![image-20220726132131613](.\21_JavaScript模块化\image-20220726132131613.png)





第二个过程被称为实例化

根据module Record这个数据结构来创建一个对象，并且专门分配一块内存空间，那么导入或者导出，会查找到的

那么创建对象的过程，被称为实例化的过程

![image-20220812080107533](.\21_JavaScript模块化\image-20220812080107533.png)



第三个过程是求值的过程

假设有一个模块，这个模块最后有一个export，在上面这个阶段将这个export导出，并且分配一块内存，这块内存其实就保存着name的值，保存着age的值，但是在第二个阶段实际保存的是空值

![image-20220812080520215](.\21_JavaScript模块化\image-20220812080520215.png)

为什么是未定义呢？因为在实例化的时候，内部代码没有运行

![image-20220812080716090](.\21_JavaScript模块化\image-20220812080716090.png)

js引擎针对模块化文件，它是分开执行的，先执行import语句和export语句

当上面import和export都执行完了之后，才会运行中间的语句，才能确定name和age的值

也就是，我知道你导出了age，导出了name，但是我不知道它的值的

之后才会执行name赋值，age赋值

之后才能知道导出的name是什么，age是什么

![image-20220812081042252](.\21_JavaScript模块化\image-20220812081042252.png)

这块内存就会保存具体值

所以有三个阶段





## 具体原理解析

![image-20220815070334814](.\21_JavaScript模块化\image-20220815070334814.png)



![image-20220815070735567](.\21_JavaScript模块化\image-20220815070735567.png)

![image-20220815071001760](.\21_JavaScript模块化\image-20220815071001760.png)

![image-20220815071103518](.\21_JavaScript模块化\image-20220815071103518.png)

就会找到里面的count，并且可以看到count是undefined

到第三个阶段运行阶段的时候，就会求值，给count赋值，给render赋值

![image-20220815071252770](.\21_JavaScript模块化\image-20220815071252770.png)

求值完之后就可以用了

![image-20220815071352258](.\21_JavaScript模块化\image-20220815071352258.png)

![image-20220815071645731](.\21_JavaScript模块化\image-20220815071645731.png)

但是不允许导入的变量去修改变量

![image-20220815071724444](.\21_JavaScript模块化\image-20220815071724444.png)



![image-20220815072453357](.\21_JavaScript模块化\image-20220815072453357.png)

这个文件的类型是module

会把这个文件解析成Module Record这个数据结构

Module Record里面有一个东西叫做 RequestedModule

这个东西会记录着刚才解析的，main.js是依赖foo的js的

foo也是一个模块，它也会解析自己的Module Record

另外的Module Record没有其他的依赖，所以不会下载其他的模块

![image-20220815072835113](.\21_JavaScript模块化\image-20220815072835113.png)

会把Module Record转成Module enveriment

会告诉Module enveriment导出的是什么东西

![image-20220815073016044](.\21_JavaScript模块化\image-20220815073016044.png)

初始的时候，导出的实际上都是undefined

然后会运行代码，给这些导出赋值（阶段三）

![image-20220815073102518](.\21_JavaScript模块化\image-20220815073102518.png)

然后给这些值取出来，但是要知道第五行的代码是不能赋值的，因为不允许在导入位置给导出的变量赋值

然后再导出的时候，一秒钟修改成另外一个值，导入也能识别到





## commonjs和esmodule相互调用

![image-20220815073808142](.\21_JavaScript模块化\image-20220815073808142.png)

能不能再commonjs中导出，在esmodule中导入呢？

或者在esmodule中导出，在commonjs中导入呢？

能或者不能，需要有一个前提，就是处于什么环境

在浏览器中：

​	不能，因为浏览器中默认不支持commonjs的

在node环境中：

​	区分不同的node版本，因为有些node版本不支持esmodule

平时开发（webpack环境)：

​	它支持两个（commonjs、esmodule), 它们支不支持相互引用呢？ 在webpack中，它们是支持相互引用的，所以在vue项目中或者react项目中，相互引用是没有问题

怎么证明呢？

下载webpack

```js
npm install webpack webpack-cli
```



然后写三个文件

​	分别用commonjs导出，esmodule导入

​	esmodule导出，commonjs导入

然后打包 npm webpack

可以发现，它们都是支持的

![image-20220815075447282](.\21_JavaScript模块化\image-20220815075447282.png)