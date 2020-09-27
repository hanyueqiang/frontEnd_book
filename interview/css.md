## css 篇

#### css 居中(某团)

水平居中元素：

- 通用方法，元素的宽高未知
  方式一：CSS3 transform

```
.parent {
    position: relative;
}
.child {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
}
```

方式二：Flex 布局

```
.parent {
    display: flex;
    justify-content: center;
}
```

适用于子元素为浮动，绝对定位，内联元素，均可水平居中。

- 居中的元素为常规文档流中的内联元素(display: inline)
  常见的内联元素有：span, a, img, input, label 等等
  此方法同样适用于 display: inline-block 的元素。

```
.parent {
    text-align: center;
}
```

- 居中的元素为常规文档流中的块元素(display: block)
  常见的块元素：div, h1~h6, table, p, ul, li 等等

方式一：设置 margin

```
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

方式二：修改为 inline-block 属性

```
.parent {
    text-align: center;
}
.child {
    display: inline-block;
}
```

- 居中的元素为浮动元素

```
.child {
    width: 100px;
    float: left;
    position: relative;
    left: 50%;
    margin-left: -50px;
}
```

- 居中的元素为绝对定位元素
  方式一：

```
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

方式二：

```
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

- 通用方法，元素的宽高未知
  方式一：CSS3 transform

```
.parent {
    position: relative;
}
.child {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}
```

方式二：Flex 布局
适用于子元素为浮动，绝对定位，内联元素，均可垂直居中。

```
.parent {
    display: flex;
    align-items: center;
}
```

居中元素为单行文本
把文字的 line-height 设为文字父容器的高度，适用于只有一行文字的情况。

```
.text {
    line-height: 200px;
    height: 200px;
}
```

- 已知元素宽高

```
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

垂直居中元素：

- 1. 绝对居中定位

```
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

- 2. 负边距居中

```
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

- 3. Transform 定位

```
.child {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

- 4. Flexbox 布局

```
.parent {
    display: flex;
    justify-content: center;
    align-items: center;
}
```

- 5. table-cell 居中

```
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
    background-color: #03f;
}
```

#### display:inline-block 为什么会有空白？(某团)

之所以存在间隙是由于元素标签之间留有空白字符造成的。
解决方案
1、第一种 消除元素标签之间的空白字符，把标签连在一起。
2、第二种：
第一步：在这些行内元素的父元素上设置 font-size 设置为 0；
第二步：在行内元素上设置正常的字体显示大小，例如:font-size:14px;
3、使用 flex 布局

#### 选择器优先级(某团)

内联 > id 选择器 > 类、属性、伪类选择器 > 标签元素、伪元素

#### css 怎么如何渲染到指定 dom 的？(某团)

css 的加载不会阻塞 DOM 的解析
css 的加载会阻塞 DOM 的渲染

## css 布局方式

#### 文档流布局（单列布局）

这是最基本的布局方式，就是按照文档的顺序一个一个显示出来，块元素独占一行，行内元素共享一行

#### 浮动布局

浮动方式布局就是使用 float 属性，使元素脱离文档流，浮动起来

#### 定位布局

我们也可以通过`position absolute`属性来进行定位

#### flex 布局(弹性盒子布局)

flex 只支持 ie 10+
垂直居中`align-items:center; justify-content：center`
水平两端对齐：`justify-content: space-between;`

#### 引入 css 有哪些方式，link 中的 media 属性是什么作用，有哪些取值
