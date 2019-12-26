# webpack

## 入口文件 Entry

-   首先执行 webpack/bin/webpack.js
-   检查 webpack 的 cli 跟 command 是否存在

```js
const CLIs = [
	{
		name: 'webpack-cli',
		package: 'webpack-cli',
		binName: 'webpack-cli',
		alias: 'cli',
		installed: isInstalled('webpack-cli'),
		recommended: true,
		url: 'https://github.com/webpack/webpack-cli',
		description: 'The original webpack full-featured CLI.',
	},
	{
		name: 'webpack-command',
		package: 'webpack-command',
		binName: 'webpack-command',
		alias: 'command',
		installed: isInstalled('webpack-command'),
		recommended: false,
		url: 'https://github.com/webpack-contrib/webpack-command',
		description: 'A lightweight, opinionated webpack CLI.',
	},
]
```

-   简易调试代码大概如下

```js
//载入webpack主体
let webpack=require('webpack');
//指定webpack配置文件
let config=require("./webpack.config.js");
//执行webpack，返回一个compile的对象，这个时候编译并未执行
let compile=webpack(config);
//运行compile，执行编译
compile.run();
```
