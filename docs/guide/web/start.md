# webpack

## 入口文件 Entry

-   入口设置

```js
module.exports = {
	entry: './main.js',
	output: {
		filename: 'bundle.js',
	},
}
```

-   多入口

```js
module.exports = {
	entry: {
		bundle1: './main1.js',
		bundle2: './main2.js',
	},
	output: {
		filename: '[name].js',
	},
}
```

## js 打包

```js
module.exports = {
	entry: './main.jsx',
	output: {
		filename: 'bundle.js',
	},
	module: {
		rules: [
			{
				test: /\.jsx?$/,
				exclude: /node_modules/,
				use: {
					loader: 'babel-loader',
					options: {
						presets: ['es2015', 'react'],
					},
				},
			},
		],
	},
}
```

## css 打包

```js
module.exports = {
	entry: './main.js',
	output: {
		filename: 'bundle.js',
	},
	module: {
		rules: [
			{
				test: /\.css$/,
				use: ['style-loader', 'css-loader'],
			},
		],
	},
}
```

## 图片资源打包

```js
module.exports = {
	entry: './main.js',
	output: {
		filename: 'bundle.js',
	},
	module: {
		rules: [
			{
				test: /\.(png|jpg)$/,
				use: [
					{
						loader: 'url-loader',
						options: {
							limit: 8192,
						},
					},
				],
			},
		],
	},
}
```

## 压缩

```js
var webpack = require('webpack')
var UglifyJsPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
	entry: './main.js',
	output: {
		filename: 'bundle.js',
	},
	plugins: [new UglifyJsPlugin()],
}
```

## 插件

```js
var HtmlwebpackPlugin = require('html-webpack-plugin')
var OpenBrowserPlugin = require('open-browser-webpack-plugin')

module.exports = {
	entry: './main.js',
	output: {
		filename: 'bundle.js',
	},
	plugins: [
		new HtmlwebpackPlugin({
			title: 'Webpack-demos',
			filename: 'index.html',
		}),
		new OpenBrowserPlugin({
			url: 'http://localhost:8080',
		}),
	],
}
```

## Loader 和 Plugin 的不同

-   Loader 直译为"加载器"。Webpack 将一切文件视为模块，但是 webpack 原生是只能解析 js 文件，如果想将其他文件也打包的话，就会用到 loader。 所以 Loader 的作用是让 webpack 拥有了加载和解析非 JavaScript 文件的能力。

```js
1. file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
2. url-loader：和 file-loader 类似，但是能在文件很小的情况下以 base64 的方式把文件内容注入到代码中去
3. source-map-loader：加载额外的 Source Map 文件，以方便断点调试
4. image-loader：加载并且压缩图片文件
5. babel-loader：把 ES6 转换成 ES5
6. css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
7. style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS。
8. eslint-loader：通过 ESLint 检查 JavaScript 代码
```

-   Plugin 直译为"插件"。Plugin 可以扩展 webpack 的功能，让 webpack 具有更多的灵活性。 在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

```js
1.define-plugin：定义环境变量
2.commons-chunk-plugin：提取公共代码
3.uglifyjs-webpack-plugin：通过UglifyES压缩ES6代码
```

## webpack 优化性能

-   压缩代码。删除多余的代码、注释、简化代码的写法等等方式。可以利用 webpack 的 UglifyJsPlugin 和 ParallelUglifyPlugin 来压缩 JS 文件， 利用 cssnano（css-loader?minimize）来压缩 css
-   利用 CDN 加速。在构建过程中，将引用的静态资源路径修改为 CDN 上对应的路径。可以利用 webpack 对于 output 参数和各 loader 的 publicPath 参数来修改资源路径
-   删除死代码（Tree Shaking）。将代码中永远不会走到的片段删除掉。可以通过在启动 webpack 时追加参数--optimize-minimize 来实现
-   提取公共代码。

## 提高构建速度

-   多入口情况下，使用 CommonsChunkPlugin 来提取公共代码
-   通过 externals 配置来提取常用库
-   利用 DllPlugin 和 DllReferencePlugin 预编译资源模块 通过 DllPlugin 来对那些我们引用但是绝对不会修改的 npm 包来进行预编译，再通过 DllReferencePlugin 将预编译的模块加载进来。
-   使用 Happypack 实现多线程加速编译
-   使用 webpack-uglify-parallel 来提升 uglifyPlugin 的压缩速度。 原理上 webpack-uglify-parallel 采用了多核并行压缩来提升压缩速度
-   使用 Tree-shaking 和 Scope Hoisting 来剔除多余代码

# js