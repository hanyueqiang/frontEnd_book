## css 篇

#### css 居中

水平居中元素：

通用方法，元素的宽高未知

- 方式一：CSS3 transform

```js
.parent {
    position: relative;
}
.child {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
}
```

- 方式二：Flex 布局

```js
.parent {
    display: flex;
    justify-content: center;
}
```

- 方式三： text-align

适用于子元素为浮动，绝对定位，内联元素，均可水平居中。
居中的元素为常规文档流中的内联元素(display: inline)
常见的内联元素有：span, a, img, input, label 等等
此方法同样适用于 display: inline-block 的元素。

```js
.parent {
    text-align: center;
}
```

- 方式四：设置 margin

居中的元素为常规文档流中的块元素(display: block)
常见的块元素：div, h1~h6, table, p, ul, li 等等

```js
.parent {
  width: 100%;
}
.child {
  width: 600px;
  height: 50px;
  margin: 0 auto;
  background: #999;
}
```

此方法只能进行水平的居中，且对浮动元素或绝对定位元素无效。

- 方式五：修改为 inline-block 属性

```js
.parent {
    text-align: center;
}
.child {
    display: inline-block;
}
```

- 方式六： 居中的元素为浮动元素

```js
.child {
    width: 100px;
    float: left;
    position: relative;
    left: 50%;
    margin-left: -50px;
}
```

- 方式七： 居中的元素为绝对定位元素

```js
.parent {
    position: relative;
}
.child {
    position: absolute;
    width: 100px;
    left: 50%;
    margin-left: -50px;
}
```

- 方式八：

```js
.parent {
    position: relative;
}
.child {
    position: absolute;
    width: 100px;
    left: 0;
    right: 0;
    margin: 0 auto;
}
```

垂直居中元素：

通用方法，元素的宽高未知

- 方式一：CSS3 transform

```js
.parent {
    position: relative;
}
.child {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}
```

- 方式二：Flex 布局

适用于子元素为浮动，绝对定位，内联元素，均可垂直居中。

```js
.parent {
    display: flex;
    align-items: center;
    justify-content: center;
}
```

- 方式三：居中元素为单行文本 line-height

把文字的 line-height 设为文字父容器的高度，适用于只有一行文字的情况。

```js
.text {
    line-height: 200px;
    height: 200px;
}
```

- 方式四：已知元素宽高

```js
.parent {
    position: relative;
}
.child{
    position: absolute;
    top: 0;
    bottom: 0;
    height: 100px;
    margin: auto 0;
}
```

- 方式五： 绝对居中定位

```js
div {
    width: 100px;
    height: 100px;
    margin: auto;
    position: fixed;
    //absolute is ok
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
}
```

- 方式六： 负边距居中

```js
.child {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    margin-left: -50px;
    margin-top: -50px;
}
```

- 方式七： Transform 定位

```js
.child {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

- 方式八： table-cell 居中

```js
.parent {
    display: table-cell;
    vertical-align: middle;
    text-align: center;
    width: 200px;
    height: 200px;
    border: 1px solid red;
}
.child {
    width: 100px;
    height: 100px;
    display: inline-block;
    background-color: #ccc;
}
```

#### display:inline-block 为什么会有空白？

之所以存在间隙是由于元素标签之间留有空白字符造成的。
解决方案
1、第一种 消除元素标签之间的空白字符，把标签连在一起。
2、第二种：
第一步：在这些行内元素的父元素上设置 font-size 设置为 0；
第二步：在行内元素上设置正常的字体显示大小，例如:font-size:14px;
3、使用 flex 布局

#### 选择器优先级

内联 > id 选择器 > 类、属性、伪类选择器 > 标签元素、伪元素

#### css 怎么如何渲染到指定 dom 的？

css 的加载不会阻塞 DOM 的解析
css 的加载会阻塞 DOM 的渲染（渲染时需等 css 加载完毕，因为 render 树需要 css 信息）

1.解析 html 为 dom 树，解析 css 为 cssom。渲染引擎开始解析 html，并将标签转化为内容树中的 dom 节点。

2. 把 dom 和 cssom 结合起来生成渲染树(render)。接着，它解析外部 CSS 文件及 style 标签中的样式信息。这些样式信息以及 html 中的可见性指令将被用来构建另一棵树——render 树。Render 树由一些包含有颜色和大小等属性的矩形组成，它们将被按照正确的顺序显示到屏幕上。

3. 布局渲染树，计算几何形状。Render 树构建好了之后，将会执行布局过程，它将确定每个节点在屏幕上的确切坐标。

4. 把渲染树展示到屏幕上。再下一步就是绘制，即遍历 render 树，并使用 UI 后端层绘制每个节点。

## css 布局方式

- 文档流布局（单列布局）

这是最基本的布局方式，就是按照文档的顺序一个一个显示出来，块元素独占一行，行内元素共享一行

- 浮动布局

浮动方式布局就是使用 float 属性，使元素脱离文档流，浮动起来

- 定位布局

我们也可以通过`position absolute`属性来进行定位

- flex 布局(弹性盒子布局)

`flex 只支持 ie 10+`
垂直居中`align-items: center; justify-content: center`
水平两端对齐：`justify-content: space-between;`

#### 引入 css 有哪些方式，link 中的 media 属性是什么作用，有哪些取值

1.行内样式
2.style 标签内样式 3.外链 link

media 属性规定被链接文档将显示在什么设备上。

media 属性用于为不同的媒介类型规定不同的样式。所有浏览器都支持值为 "screen"、"print" 以及 "all" 的 media 属性。
