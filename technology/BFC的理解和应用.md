## BFC 的理解和应用

> 定义：全称是 Block Formatting Context，即块格式化上下文；BFC 是一个独立的布局环境，其中的元素布局是不受外界的影响，并且在一个 BFC 中，块盒与行盒（行盒由一行中所有的内联元素所组成）都会垂直的沿着其父元素的边框排列

一个块格式化上下文由以下之一创建：

- 根元素`HTML`或其它包含它的元素
- 浮动元素 (元素的 `float` 不是 `none`)
- 绝对定位元素 (元素具有 `position` 为 `absolute`或 `fixed`)
- 内联块 (元素具有 `display: inline-block`)
- 表格单元格 (元素具有 `display: table-cell`，HTML 表格单元格默认属性)
- 表格标题 (元素具有 `display: table-caption`, HTML 表格标题默认属性)
- 具有`overflow` 且值不是 `visible` 的块元素
- `display: flow-root`
- `column-span: all` 应当总是会创建一个新的格式化上下文，即便具有 `column-span: all` 的元素并不被包裹在一个多列容器中

在工作和面试中，总会碰到 BFC，而 BFC 的解释又很难理解，这时可以结合一个例子进行讲解，以下应用场景均可作为触发 BFC 的使用案例

#### 使用场景

- 外边距合并
- 浮动元素或定位元素脱离文档流导致父元素高度丢失
- 清除浮动
- 兄弟元素其中一个元素浮动，其他兄弟元素占有该元素位置，需要设置`margin`进行隔离

以上为开发中常用场景，还有其他场景不一一列举，均可使用触发 BFC 解决

#### 应用一：父元素高度塌陷/外边距合并

1.在父元素里子元素`div`设置`marginTop`，导致父元素整体高度塌陷

```js
<style>
  * {
    margin: 0;
    padding: 0;
  }
  .a1_1 {
    margin-top: 20px;
    margin-bottom: 20px;
    border: 1px solid blue;
    width: 200px;
  }
  .bfc {
    overflow: hidden;
  }
</style>

<body>
  <div class="a1">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
</body>

// 解决办法父元素添加`overflow: hidden`触发BFC
<body>
  <div class="a1 bfc">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
</body>
```

2.在兄弟元素`div`设置`marginTop`，`marginBottom`导致相邻元素外边距合并

```js
<style>
  * {
    margin: 0;
    padding: 0;
  }
  .a1_1 {
    margin-top: 20px;
    margin-bottom: 20px;
    border: 1px solid blue;
    width: 200px;
  }
  .a1_2 {
    margin-top: 20px;
    border: 1px solid red;
    width: 200px;
  }
  .bfc {
    overflow: hidden;
  }
</style>
// a1_1和a1_2margin合计为20px
<body>
  <div class="a1 bfc">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
</body>

// 解决办法在其中一个元素上添加一层div同时添加`overflow: hidden`触发BFC
<body>
  <div class="a1 bfc">
    <div class="a1_1">context1</div>
    <div class="bfc">
      <div class="a1_2">context2</div>
    </div>
  </div>
</body>
```

#### 应用二：浮动元素或定位元素脱离文档流导致父元素高度丢失

```js
<style>
  .a1_1 {
    margin-top: 20px;
    margin-bottom: 20px;
    border: 1px solid blue;
    width: 200px;
    float: left;
  }
  .a1_2 {
    margin-top: 20px;
    border: 1px solid red;
    width: 200px;
    float: left;
  }
  .bfc {
    overflow: hidden;
  }
</style>
// 子元素均浮动、父元素a1的高度是0
<body>
  <div class="a1">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
</body>
// 解决办法在父元素上添加添加`overflow: hidden`触发BFC，父元素a1高度有了
<body>
  <div class="a1 bfc">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
</body>
```

#### 应用三：清除浮动

```js
<style>
  .a1_1 {
    margin-top: 20px;
    margin-bottom: 20px;
    border: 1px solid blue;
    width: 200px;
    float: left;
  }
  .a1_2 {
    margin-top: 20px;
    border: 1px solid red;
    width: 200px;
    float: left;
  }
  .bfc {
    overflow: hidden;
  }
  .clear {
    clear: both;
  }
</style>
// 子元素均浮动，由于父元素无高度，后面元素位置会靠前排列，这并不是我们想要的
<body>
  <div class="a1">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
  <div>
    other content
  </div>
</body>
// 解决方法1：清除浮动，在浮动元素同级最后添加一个div设置清除浮动样式`clear:both`、不触发BFC
<body>
  <div class="a1">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
    <div class="clear"></div>
  </div>
  <div>
    other content
  </div>
</body>
// 解决方法2: 给浮动元素的父元素设置`overflow:hidden`，触发BFC
<body>
  <div class="a1 bfc">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
  <div>
    other content
  </div>
</body>
```

#### 应用四：兄弟元素其中一个元素浮动，其他兄弟元素占有该元素位置

```js
<style>
  .a1_1 {
    margin-top: 20px;
    margin-bottom: 20px;
    border: 1px solid blue;
    width: 200px;
    float: left;
  }
  .a1_2 {
    margin-top: 20px;
    border: 1px solid red;
    height: 100px;
  }
  .bfc {
    overflow: hidden;
  }
  .clear {
    clear: both;
  }
</style>
// a1_1浮动, a1_2会覆盖a1_1的位置，可设置margin-left解决
<body>
  <div class="a1 bfc">
    <div class="a1_1">context1</div>
    <div class="a1_2">context2</div>
  </div>
  <div>
    other content
  </div>
</body>

// 解决方法: 给非浮动元素设置`overflow:hidden`，触发BFC
<body>
  <div class="a1 bfc">
    <div class="a1_1">context1</div>
    <div class="a1_2 bfc">context2</div>
  </div>
  <div>
    other content
  </div>
</body>
```

#### 总结

`BFC`就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。同时外面的元素也不会影响容器元素。所以当`BFC`外部存在浮动元素时，不影响`BFC`内部元素布局，当`BFC`内部有浮动时，为了不影响外部元素的布局，`BFC`计算高度时会包括浮动的高度。避免`margin`重叠
