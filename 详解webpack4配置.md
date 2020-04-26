> webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle

### 基础篇

#### 安装 webpack

```
yarn add webpack webpack-cli -D
```

#### 配置 webpack

webpack4 支持 0 配置，可不引入配置文件，但要按照 webpack 规范目录开发，有局限性，大多是需要手动设置，在根目录新建一个 webpack.config.js 或 webpackfile.js，同我一起一步一步写入配置，从此走向人生巅峰！

```
// 目录 webpack.config.js

// webpack 是基于node实现的，所以我们可以使用node自带的一些模块
let path = requore('path');

module.exports = {
  // 打包模式 两种模式:development和production,默认是production
  mode: 'development',
  // 入口文件
  entry: './src/index.js',
  // 打包完成后输出文件位置和文件名配置
  output: {
    // 打包后文件名,加hash解决缓存问题,hash戳太长可设置8位
    filename: 'bundle.[hash:8].js',
    // 路径必须是绝对路径
    path: path.resolve(__dirname, 'dist'),
  }
}
```

#### 自定义配置文件名称

有时候我们可能定义多个 config 比如开发环境和生产环境对配置，这时要在 package.json 目录下 scripts 内配置

```
// 目录 package.json

// --config  后面跟自定义配置文件名称
"scripts": {
  "build": "webpack --config webpack.config.production.js",
}
```

#### 开发服务器配置

在开发环境我们需要启动一个本地服务，安装 webpack-dev-server 插件

```
yarn add webpack-dev-server -D


// 修改package.json下对dev启动配置
"scripts": {
  "dev": "webpack-dev-server --config webpack.config.production.js",
}

// 在webpack.config.js内添加对devServer配置

module.exports = {
  devServer: {
    // 端口号
    port: 3000,
    // 显示打包进度
    progress: true,
    // 静态服务目录
    contentBase: './dist',
    // 自动打开浏览器
    open: true,
    // 启动gzip压缩
    compress: true,
  },
  ...
}
```

#### 生成 html 模版配置

把项目中 js/css/img 打包到 index.html 内，引入插件 html-webpack-plugin

```
yarn add html-webpack-plugin -D
```

在 webpack.config.js 中 plugins 中配置

```
目录 webpack.config.js

let HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      // 项目中对模版html
      template: './src/index.html',
      // 打包后的文件名
      filename: 'index.html',
      // 压缩html配置
      minify: {
        // 删除属性中双引号
        removeAttributeQuotes: true,
        // 折叠空行
        collapseWhitespace: true,
      },
      // 打包后的文件带hash戳 例如：vender.js?5c5c0e9b436ca75794a,项目中不建议开启
      hash: true
    })
  ],
  ...
}
```

#### css/less/sass 样式处理

- 处理 css 文件,接续@import 语法，使用 css-loader
- 把 css 插入到 head 标签内使用 style-loader，缺点把样式打入 head 标签内，如样式太多造成阻塞
- 抽离 css 文件 使用 mini-css-extract-plugin
- 自动加前缀兼容各种浏览器 postcss-loader autoprefixer
- 处理 less 文件使用 less less-loader
- 处理 sass 文件使用 node-sass sass-loader
- 压缩 css 文件使用 optimize-css-assest-webpack-plugin

> `loader` 的执行顺序，从右往左执行，从下向上执行

安装`loader`

```
yarn add css-loader style-loader less less-loader mini-css-extract-plugin postcss-loader autoprefixer optimize-css-assest-webpack-plugin -D
```

在 webpack.config.js 中 module 下进行 loader 配置

```
// webpack.config.js

let MiniCssExtractPlugin = require('mini-css-extract-plugin');
let OptimizeCssAssestsPlugin = require('optimize-css-assest-webpack-plugin');
let UgifyJsPlugin = require('uglifyjs-webpack-plugin');
module.exports = {
  // 优化项
  optimization: {
    minimizer: [
      // 压缩js
      new UgifyJsPlugin({
        // 是否使用缓存
        catch: true,
        // 是否采用多进程打包
        parallel: true,
        // 压缩完js产生源码映射，方便调试
        sourceMap: true
      })
      // 压缩css
      new OptimizeCssAssestsPlugin()
    ]
  },
  // 模块
  module: {
    // 规则
    rules: [
      {
        test: /\.css$/,
        use: [
          // 抽离成css文件
          MiniCssExtractPlugin.loader,
          'css-loader',
          // 先加前缀，后使用css-loader处理
          'postcss-loader',
        ]
      },
      {
        test: /\.less$/,
        use: [
          // {
            // loader: 'style-loader',
            // options: {
              // 插入head标签顶部
              // insertAt: 'top
            // }
          // },
           // 抽离成css文件
          MiniCssExtractPlugin.loader,
          // 解析路径，处理@import语法
          'css-loader',
          // 先加前缀，后使用css-loader处理
          'postcss-loader',
          // less-loader调用less进行转化
          'less-loader'
        ]
      },
    ],
    plugins: [
      new MiniCssExtractPlugin({
        filename: 'main.css'
      }),
    ]
    ...
  }
}
```

#### js 处理

```
// 转化 es6 语法
fn =()=> {
  console.log(111)
};
fn();
```

安装插件

```
yarn add babel-loader @babel/core @babel/preset-env -D
```

```
module: {
  rules:[
    {
      test: /\.js$/,
      use: {
        loader: 'babel-loader',
        // 用babel-loader需要把es6转化es5
        options: {
          presets: ['@babel/preset-env']
        }
      },
      plugins: [
        ['@babel/plugin-proposal-decorators'],
        ['@babel/plugin-proposal-class-properties'],
        '@babel/plugin-transform-runtime'
      ]
    }
  ]
}
```

#### eslint 语法校验

```
module: {
  rules:[
    {
      test: /\.js$/,
      use: {
        loader: 'eslint-loader',
        options: {
          // 强制在处理js前执行 ，之后执行post
          enforce: 'pre'
        }
      }
    }
  ]
}
```

#### 第三方模块使用

使用 jquery

```
plugins: [
  //在每个模块注入$
  new webpack.ProvidePlugin({
    jquery: '$',
  })
],
// 引入cdn不希望打包
externals: {
  jquery: '$'
}
```

#### 图片处理

安装插件

```
yarn add url-loade -D
```

配置规则

```
{
  test: /\.(png|jpg|gif)/,
    use: {
      loader: {
        // 做一个限制 小于多少k用base64转换
        loader: "url-loader",
        options: {
          //小于200k 使用base64
          limit: 200 * 1024,
          //输出路径
          outputPath: "img",
          //图片指定路径，例如放入cdn
          publicPath: "",
        },
      },
    },
},
```

### 配置篇

。。。
