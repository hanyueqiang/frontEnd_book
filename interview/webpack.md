## 项目工程化篇

#### treeshaking 原理

tree shaking 其实很好理解：一颗树，用力摇一摇，枯萎的叶子会掉落下来。剩下的叶子都是存活的

> Tree shaking 是一种通过清除多余代码方式来优化项目打包体积的技术，专业术语叫 Dead code elimination

如何使用 Tree shaking
从 webpack 2 开始支持实现了 Tree shaking 特性，webpack 2 正式版本内置支持 ES2015 模块（也叫做 harmony 模块）和未引用模块检测能力。
新的 webpack 4 正式版本，扩展了这个检测能力，通过 package.json 的 sideEffects 属性作为标记，向 compiler 提供提示，表明项目中的哪些文件是 “pure(纯的 ES2015 模块)”，由此可以安全地删除文件中未使用的部分。在 webpack4 项目中,只需要将 mode 设置为 production 即可开启 tree shaking

原理： 1.利用 ES6 模块的特点:
只能作为模块顶层的语句出现
import 的模块名只能是字符串常量，不能动态引入模块
import binding 是 immutable 的，引入的模块不能再进行修改 2.代码擦除： uglify 阶段删除无用代码

总结
tree shaking 不支持动态导入（如 CommonJS 的 require()语法），只支持纯静态的导入（ES6 的 import/export）
webpack 中可以在项目 package.json 文件中，添加一个 “sideEffects” 属性,手动指定由副作用的脚本

#### 按需加载的原理

​ 当页面中一个文件过大并且还不一定用到的时候，我们希望在使用到的时才开始加载，这就是按需加载。要实现按需加载，我们一般想到的方法：动态创建 script 标签，并将 src 属性指向对应的文件路径。但是在实现过程中，存在下面问题：
怎么保证相同的文件只加载一次？
怎么判断文件加载完成？
文件加载完成后，怎么通知所有引入文件的地方？

​webpcak 的按需加载已经完美解决了上述问题，本着学习的态度，我决定深入探究一下 webpack 按需加载的实现原理。

步骤：

1.开始异步加载 js

2.定义 installedChunks,存储异步 js 的 promise 回调

3.发起 jsonp 请求，异步加载 js,并定义失败成功回调

4.异步加载成功后，异步 js 执行被重写的 push 方法

5.重写 push 方法中执行 promise 的 resolve 回调

6.执行加载成功回调

1、怎么保证相同的文件只加载一次？
​ 答：定义 installedChunks 对象，存储异步 js 的 promise 回调，如果已经加载过，则返回一个空数组的 promise.all([])，如果在加载过程中，则返回已经存储过的此文件对应的 promise。

2、怎么判断文件加载完成？
​ 答：1、在主文件中定义一个全局数组，并重写其 push 方法，在异步文件中执行此全局数组的 push 方法。
​ 2、在重写的方法中执行 promise 的 resolve 回调。

#### 双向数据绑定原理

目前几种主流的 mvc(vm)框架都实现了单向数据绑定，而我所理解的双向数据绑定无非就是在单向绑定的基础上给可输入元素（input、textare 等）添加了 change(input)事件，来动态修改 model 和 view，并没有多高深。所以无需太过介怀是实现的单向或双向绑定

发布者-订阅者模式（backbone.js）
脏值检查（angular.js）
数据劫持（vue.js）

数据劫持: vue.js 则是采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty()来劫持各个属性的 setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

##### linux 常用命令(某站)
