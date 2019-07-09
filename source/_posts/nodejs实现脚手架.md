---
title: nodejs实现脚手架
date: 2019-07-09 14:14:59
tags:
---

### 前言

Vue 官网推出vue-cli 功能很强大，使用很方便，我们就想这样一个脚手架是怎么实现的，查阅了相关资料后，准备做了一简版的脚手架，熟悉一下大概流程，在工作中，需要开发一个脚手架，用于给相关用户提供相关的开发便利性。



### 目标

1. 开发一个简单的脚手架，能够提供给用户进行安装。

2. 能够输出相关提示。

3. 对用户文件进行读写操作。

4. 在脚手架中使用Shell脚本。


### 相关插件

```
vue-cli 	// vue 脚手架
commander 
inquirer	// 交互插件
chalk 		//是一个日志的样式库

```



### commander插件

##### ·version (作用：定义命令程序的版本号)

```
用法示例：.version('0.0.1', '-v, --version')
参数解析：
	① 版本号<必须>
	② 自定义标志<可省略>：默认为 `-V` 和 `--version`
```



##### ·option (作用：用于定义命令选项)

```javascript
用法示例：.option('-n, --name<path>', 'name description', 'default name')
例：.option('-a, --aaa', 'aaaaa')
参数解析：
	① 自定义标志<必须>：分为长短标识，中间用逗号、竖线或者空格分割；标志后面可跟必须参数或可选参数，前者用<> 包含，后者用 [] 包含
	② 选项描述<省略不报错>：在使用 --help 命令时显示标志描述
	③ 默认值<可省略>
```



##### ·command (作用：添加命令名称)

```
用法示例：.command('rmdir <dir> [otherDirs...]', 'install description', opts)
参数解析：
	① 命令名称<必须>：命令后面可跟用 <> 或 [] 包含的参数；命令的最后一个参数可以是可变的，像实例中那样在数组后面加入 ... 标志；在命令后面传入的参数会被传入到 action 的回调函数以及 program.args 数组中
	② 命令描述<可省略>：如果存在，且没有显示调用action(fn)，就会启动子命令程序，否则会报错
	③ 配置选项<可省略>：可配置noHelp、isDefault等
```



##### ·description (作用：定义命令的描述)

```
用法示例：.description('rmdir desc')
```



##### ·action (作用：定义命令的回调函数)

```
用法示例：.action(fn)
```



##### ·parse (作用：用于解析process.argv，设置options以及触发commands)

```
用法示例：.parse(process.argv)
```

### 如何实现一个简单的Node.js脚手架

#### 开发脚手架

脚手架的开发最开始过程与普通的前端项目相同，需要一个入口文件`command.js`和配置文件`package.json`。

与其他配置文件不同的是，你需要在`command.js`文件第一行增加如下声明：

command.js

```javascript
#!/usr/bin/env node				// 指定脚本的执行程序
```

package.json

```javascript
{
  "name": "hwl-cli",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "bin": {
    "hwl-cli": "command.js"			// 同时需要在package.json文件中加上这一项
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "commander": "^2.19.0"			// 是一个提供用户命令行输入和参数解析的强大功能
  }
}

```

在配置文件中增加了此项后，只需要在配置文件根目录下执行`npm link`命令，即可使用`hlw-cli`命令来查看加载的aa-cli脚手架, 在终端输入`hwl-cli` 不报错，说明已成功。

如果你发布了你的脚手架，那么在其他用户使用命令`npm install -g aa-cli`之后，便可以在全局下使用你的脚手架了。



#### 对用户进行提示

在对注释和命令进行提示中，我们需要使用到`commander`包，使用`npm install commander`即可进行安装。（如果NPM版本低于5，则需要添加--save参数保证更新`package.json`配置文件）。

`commander`是一个提供用户命令行输入和参数解析的强大功能。有需要的可以阅读相关的库文档。在这里我介绍两个用的最多的方法。



#### option(注：需安装commander插件才能使下面功能正常使用)

能够初始化自定义的参数对象，设置关键字和描述，同时还可以设置读取用户输入的参数。具体用法如下：

```
const commander = require('commander');

commander.version('1.0.0')
    .option('-a, --aaa', 'aaaaa')
    .option('-b, --bbb', 'bbbbb')
    .option('-c, --ccc [name]', 'ccccc')
    .parse(process.argv);


if (commander.aaa) {
    console.log('aaa');
}

if (commander.bbb) {
    console.log('bbb');
}

if (commander.ccc) {
    console.log('ccc', commander.ccc);
}
```

### 参考资料
