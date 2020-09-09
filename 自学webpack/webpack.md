# webpack

## 1.概念

### 1.1 入口

#### 1.1.1 单个入口语法

语法：`entry: string|Array<string>`

```javascript
webpack.config.js

module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

`entry` 属性的单个入口语法，是下面的简写：

```javascript
const config = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
};
```

#### 1.1.2 对象语法

用法：`entry: {[entryChunkName: string]: string|Array<string>}`

```javascript
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
};
```

### 1.2 出口

#### 1.2.1 单个入口起点

```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```

#### 1.2.2 多个入口起点

如果配置创建了多个单独的 "chunk"（例如，使用多个入口起点或使用像 CommonsChunkPlugin 这样的插件），则应该使用[占位符(substitutions)](https://www.webpackjs.com/configuration/output#output-filename)来确保每个文件具有唯一的名称。

```javascript
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
}

// 写入到硬盘：./dist/app.js, ./dist/search.js
```



### 1.3 loader

loader 用于对模块的源代码进行转换。loader 可以使你在 `import` 或"加载"模块时预处理文件。因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 `import` CSS文件！

#### 1.3.1 安装loader

```javascript
npm install --save-dev css-loader
npm install --save-dev ts-loader
```

#### 1.3.2 配置loader

test属性：用于标识出应该被对应的loader进行转换的某个或某些文件

use属性：表示进行转换时，应该使用哪个loader

```javascript
const path = require('path');

const config = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};

module.exports = config;
```

### 1.4 插件（plugin）

loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。[插件接口](https://www.webpackjs.com/api/plugins)功能极其强大，可以用来处理各种各样的任务。

想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建它的一个实例。

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

const config = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

### 1.5 模式

通过选择 `development` 或 `production` 之中的一个，来设置 `mode` 参数，你可以启用相应模式下的 webpack 内置的优化

``` javascript
module.exports = {
  mode: 'production'
};
```

## 2. 配置

### 2.1 基本配置

webpack的配置文件是导出一个对象的JavaScript文件

因为webpack配置是标准的Nodejs CommonJS模块，可以：

- 通过`require(...)`导入其他文件

- 通过`require(...)`使用npm的工具函数

- 使用JavaScript控制流表达式

- 对常用值使用常量或变量

- 编写并执行函数来生成部分配置

  ```javascript
  var path = require('path');
  
  module.exports = {
    mode: 'development',
    entry: './foo.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'foo.bundle.js'
    }
  };
  ```

### 2.2 多种配置类型

#### 2.2.1 导出为一个函数

最终，你会发现需要在[开发](https://www.webpackjs.com/guides/development)和[生产构建](https://www.webpackjs.com/guides/production)之间，消除 `webpack.config.js` 的差异。（至少）有两种选项：

作为导出一个配置对象的替代，还有一种可选的导出方式是，从 webpack 配置文件中导出一个函数。该函数在调用时，可传入两个参数：

- 环境对象(environment)作为第一个参数。有关语法示例，请查看[CLI 文档的环境选项](https://www.webpackjs.com/api/cli#environment-options)。 一个选项 map 对象（`argv`）作为第二个参数。这个对象描述了传递给 webpack 的选项，并且具有 [`output-filename`](https://www.webpackjs.com/api/cli/#output-options) 和 [`optimize-minimize`](https://www.webpackjs.com/api/cli/#optimize-options) 等 key。

  ```javascript
  -module.exports = {
  +module.exports = function(env, argv) {
  +  return {
  +    mode: env.production ? 'production' : 'development',
  +    devtool: env.production ? 'source-maps' : 'eval',
       plugins: [
         new webpack.optimize.UglifyJsPlugin({
  +        compress: argv['optimize-minimize'] // 只有传入 -p 或 --optimize-minimize
         })
       ]
  +  };
  };
  ```

#### 2.2.2 导出一个Promise

webpack 将运行由配置文件导出的函数，并且等待 Promise 返回。便于需要异步地加载所需的配置变量。

```javascript
module.exports = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({
        entry: './app.js',
        /* ... */
      })
    }, 5000)
  })
}
```

#### 2.2.3 导出多个配置对象

作为导出一个配置对象/配置函数的替代，你可能需要导出多个配置对象（从 webpack 3.1.0 开始支持导出多个函数）。当运行 webpack 时，所有的配置对象都会构建。例如，导出多个配置对象，对于针对多个[构建目标](https://www.webpackjs.com/configuration/output#output-librarytarget)（例如 AMD 和 CommonJS）[打包一个 library](https://www.webpackjs.com/guides/author-libraries) 非常有用。

```javascript
module.exports = [{
  output: {
    filename: './dist-amd.js',
    libraryTarget: 'amd'
  },
  entry: './app.js',
  mode: 'production',
}, {
  output: {
    filename: './dist-commonjs.js',
    libraryTarget: 'commonjs'
  },
  entry: './app.js',
  mode: 'production',
}]
```

## 3. 入口和上下文

entry 对象是用于 webpack 查找启动并构建 bundle。其上下文是入口文件所处的目录的绝对路径的字符串。

## `context`

```
string
```

基础目录，**绝对路径**，用于从配置中解析入口起点(entry point)和 loader

```javascript
context: path.resolve(__dirname, "app")
```

## `entry`

```
string | [string] | object { <key>: string | [string] } | (function: () => string | [string] | object { <key>: string | [string] })
```

起点或是应用程序的起点入口。从这个起点开始，应用程序启动执行。如果传递一个数组，那么数组的每一项都会执行。

动态加载的模块**不是**入口起点。

简单规则：每个 HTML 页面都有一个入口起点。单页应用(SPA)：一个入口起点，多页应用(MPA)：多个入口起点。

```javascript
entry: {
  home: "./home.js",
  about: "./about.js",
  contact: "./contact.js"
}
```

