## 默认打包 src/index.js -> dist/man.js

## webpack.config.js

```js
const path = require('path');

module.exports = {
entry: './src/main.js',
output: {
filename: 'bundle.js',
path: path.join(\_\_dirname,'dist')，
publicPath: 'dist/'
}
}
```

## webpack 工作模式

production 默认 压缩

配置 mode 工作模式

## webpack 运行原理

模块私有作用域
以 js 为入口，解析推断依赖 最后形成依赖树 递归依赖树 加载器进行加载 打包

loader 机制是核心

## 开发一个 loader

loader 是管道的概念 可以组合
markdown-loader

```js
const marked = require("marked");
module.exports = (source) => {
  return marked(source);
};
```

## webpack 插件机制

plugin
clean-webpack-plugin
