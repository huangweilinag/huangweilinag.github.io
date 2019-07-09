---
title: vscode插件开发
date: 2019-07-09 14:49:49
tags:
---



### 前言

工欲善其事必先利其器。vscode作为优秀的开发工具，给我的日常开发工作提供了极大的便利。其拓展机制更是如此。在我们日常开发中使用太多别人开发的插件如ESlint、SFTP、Todo Tree等等，今天我们就来做一个自已的vscode插件。



Demo 指令集

| 指令                   | 说明                         | 快捷键  | 右键菜单               |
| ---------------------- | ---------------------------- | ------- | ---------------------- |
| hwl-show01             | 显示info信息                 | shift+f | hwl-show1              |
| hwl-show02             | 显示错误信息                 | -       |                        |
| hwl-show03             | 状态栏中显示                 | -       |                        |
| hwl-show04             | 显示对话框                   | -       |                        |
| hwl-cursorTop          | 光标回到文本头部             |         | hwl-cursorTop          |
| hwl-openWebview        | 打开一个html文件             |         |                        |
| hwl-showImg            | 打开一个html文件，并显示图片 |         |                        |
| hwl-getCurrentFilePath | 获取文件路径                 |         | hwl-getCurrentFilePath |
| hwl-selection          | 获取选择的是第几行到第几行   |         | hwl-selection          |
| hwl-aligner            | 格式化注释                   |         | hwl-aligner            |





### 一、创建帐号

进入[vscode 插件官网](https://marketplace.visualstudio.com/) 创建帐号, 用于在插件市场上发布

![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode03.png)



### 二、安装脚手架工具

官方推荐使用Yeoman 和 VS Code Extension Generator

```
$ npm install -g yo generator-code		// 安装vscode插件生产工具
```



### 三、新建项目

初始化项目

```
$ yo code
```

进行简单设置

![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode01.png)

初始化完成后的项目结构

<img src="https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode02.png" width="30%" height="30%">

文件说明

```
├── .vscode      // 资源配置文件
├── CHANGELOG.md // 更改记录文件，会展示到vscode插件市场
├── extension.js // 拓展源代码文件
├── jsconfig.json
├── package.json // 资源配置文件
├── README.md    // 插件介绍文件，会展示到vscode插件市场
├── test         // 测试文件
└── vsc-extension-quickstart.md
```

### 四、开发

##### 1. package.json 

告知vscode，自己定义的事件，和触发事件的方式

```
{
	"name": "hwl-aligner",			// 插件名
	"displayName": "hwl-aligner",	// 显示名称
	"description": "A tool for aligning the inline trailing comment",	// 描述
	"version": "0.0.4",				// 版本号
	"publisher": "huangweiliang",	// 发布人，在插件市场官网创建的发布人id
	"icon": "icon/icon.jpg",		// 插件图标，用于在插件市场展示的icon
	"repository": {
		"type": "git",
		"url":"https://gitee.com/huangweiliang/hwl-vscode.git"
	},
	"engines": {
		"vscode": "^1.33.1"			// vscode版本号
	},
	"categories": [
		"Other"						// vscode插件类别，会在插件市场的对应类别中展示
	],
	"activationEvents": [	// "*" 表示在vscode启动的时候，就启动插件了。不太友好，谨慎使用。
		"onCommand:extension.hwlaligner"
	],
	"main": "./extension.js",		// 插件的主入口
	"contributes": {
		"commands": [{				// 命令
      		"command": "extension.hwlaligner",	// 插件注册的类名
			"title": "Hwl Aligner"				// 插件在命令面包的展示名称
		}],
        "keybindings": [			// 快捷键绑定
            {
                "command": "extension.sayHello",
                "key": "shift+f10",
                "mac": "shift+f10",			// mac shift+fn+f9
                "when": "editorTextFocus"
            }
        ],
        "menus": {					// 配置右键菜单
			"editor/context": [		// 是key值，定义这个菜单出现在哪里，详情请参考「配置自定义菜单」
					{
							"when": "editorFocus",					// 控制菜单合适出现
							"command": "extension.hwl007",			// 定义菜单被点击后要执行什么操作
							// 分组 navigation是一个永远置顶的分组，后面的@6是人工进行组内排序
							"group": "navigation@6"	,
							// 定义备用命令，按住alt键打开菜单时将执行对应命令(可省略)
                            "alt": "markdown.showPreviewToSide"
					}
			]
		}
	},
	"scripts": {
		"postinstall": "node ./node_modules/vscode/bin/install",
		"test": "node ./node_modules/vscode/bin/test"
	},
	"devDependencies": {
		"typescript": "^3.1.4",
		"vscode": "^1.1.25",
		"eslint": "^4.11.0",
		"@types/node": "^8.10.25",
		"@types/mocha": "^2.2.42"
	}
}
```

配置editor/context 菜单效果

![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode09.png)

##### 2. extension.js 程序入口

```javascript
const vscode = require('vscode');		// 引入文件

function activate(context) {
    // 绑定事件
	let disposable = vscode.commands.registerCommand('extension.helloWorld', function () {
		vscode.window.showInformationMessage('Hello World123!');

	});

	context.subscriptions.push(disposable);
}

// 在插件被激活的时候，这个方法会被调用activate
exports.activate = activate;
function deactivate() {}

module.exports = {
	activate,
	deactivate
}

```

##### 3. 一个插件实现多条命令

```
// extension.js
function activate(context) {
    // 实现指令1功能
	let disposable1 = vscode.commands.registerCommand('extension.hwl1', function () {
		vscode.window.showInformationMessage('我是指令1');
	});
	// 实现指令2功能
	let disposable2 = vscode.commands.registerCommand('extension.hwl2', function () {
		vscode.window.showInformationMessage('我是指令2');
	});
	
	// 实现指令3功能，获取url需要把这个方法绑定在右键菜单上才有值
	let disposable2 = vscode.commands.registerCommand('extension.getCurrentFilePath', (url) => {
        vscode.window.showInformationMessage(`当前文件(夹)路径是：${url ? url.path : '空'}`);
    });

	context.subscriptions.push(disposable1);	// 加入
	context.subscriptions.push(disposable2);	// 加入
	context.subscriptions.push(disposable3);	// 加入
}

// package.json
// 注册命令
"commands": [
    {
    	"command": "extension.hwl1",
    	"title": "hwl1"
    },
    {
    	"command": "extension.hwl2",
    	"title": "hwl2"
    }
]
```



##### 4. 常用指令

进入[官网API](https://code.visualstudio.com/api/references/vscode-api)查看

提示、显示、对话框使用：

```javascript
vscode.window.showInformationMessage('我是info信息！');
vscode.window.showErrorMessage('我是错误信息！');
vscode.window.showInformationMessage('是否打开网页？', '是', '否', '不再提示').then(result => {
    if (result === '是') {
    // exec(`open 'https://haoji.me'`);
    } else if (result === '不再提示') {
    // 其它操作
    }
});
vscode.window.setStatusBarMessage('状态栏中显示');	
```

![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode11.png)

![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode13.png)

![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode12.png)

![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode10.png)



光标使用：

```
cursorTop: 移动到文章首
cursorBottom: 移动到文章尾
cursorRight: 光标向右移，相当于emacs的forward-char
cursorLeft: 光标左移，相当于emacs的backward-char
cursorDown: 向下移一行，相当于emacs的next-line
cursorUp: 向上移一行，相当于emacs的previous-line
cursorLineStart: 移至行首，相当于emacs的move-beginning-of-line
cursorLineEnd: 移至行尾，相当于emacs的move-end-of-line

// 使用
vscode.commands.executeCommand('cursorTop');
```

webview使用：

```javascript
let openWebview = vscode.commands.registerCommand('extension.openWebview', function () {
    const panel = vscode.window.createWebviewPanel(
        'testWebview', // viewType
        "WebView演示", // 视图标题
        vscode.ViewColumn.One, // 显示在编辑器的哪个部位
        {
            enableScripts: true, // 启用JS，默认禁用
            retainContextWhenHidden: true, // webview被隐藏时保持状态，避免被重置
         }
    );
    panel.webview.html = `<html><body>你好，我是Webview</body></html>`
});
```

##### 5.配置自定义菜单

目前插件可以给以下场景配置自定义菜单：

```
资源管理器上下文菜单 - explorer/context
编辑器上下文菜单 - editor/context
编辑标题菜单栏 - editor/title
编辑器标题上下文菜单 - editor/title/context
调试callstack视图上下文菜单 - debug/callstack/context
SCM标题菜单 -scm/title
SCM资源组菜单 -scm/resourceGroup/context
SCM资源菜单 -scm/resource/context
SCM更改标题菜单 -scm/change/title
左侧视图标题菜单 -view/title
视图项菜单 -view/item/context
控制命令是否显示在命令选项板中 - commandPalette
```

##### 6.加载本地资源

```
// 加载本地图片
// context 为上下文，即function activate(context) {}	
const imgUrl = path.join(context.extensionPath, './img01.jpg')	
const diskPath = vscode.Uri.file(imgUrl);
const imagePath = diskPath.with({ scheme: 'vscode-resource' }).toString();
	
```

##### 7. 获取编辑器内容

```
// 获取所有选中行信息
let selection = vscode.window.activeTextEditor.selection;
console.log(selection)
```

<img src="https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode14.png" width="30%" height="30%">

##### 8.以文件的方式引入

方法一： hwlaligner.js

```javascript
const vscode = require('vscode');
module.exports = {
  fun: function() {
      vscode.window.showInformationMessage('hwlaligner');
  }
```

extension.js

```javascript
const hwlaligner = require('./hwlaligner')

vscode.commands.registerCommand('extension.hwl-aligner', hwlaligner.fun)
```

方法二： hwlhttp.js

```javascript
const vscode = require('vscode');
var http = require("http");

module.exports = function() {
  vscode.window.showInformationMessage('http');
}
```

extension.js

```javascript
const hwlhttp = require('./hwlhttp')

let hwlHttp = vscode.commands.registerCommand('extension.hwl-http', hwlhttp)
```

##### 9. 创建代码片段

`package.json`文件新增如下：

```
"contributes": {
    "snippets": [
        {
            // 代码片段作用于那种语言
            "language": "javascript",
            // 片段文件路径
            "path": "./snippets/javascript.json"
        }
    ]
}
```

然后创建一个`snippets/javascript.json`文件，我们这里先简单点，比如创建一个for循环：

```
{
    "for循环": {
        "prefix": "for",
        "body": [
          "for (const ${2:item} of ${1:array}) {",
          "\t$0",
          "}"
        ],
        "description": "for循环"
    }
}
```

解释如下：

- `for循环`：`snippets`的名字；
- `prefix`：输入什么单词触发代码片段；
- `body`：一个数组，存放代码片段的内容，每一行一个字符串；
- `description`：片段的描述；
- `${1:xxx}`占位符，数字表示光标聚焦的顺序，1表示默认光标落在这里，按下回车或者tab跳到2的位置，以此类推，`xxx`表示此位置的默认值，可省略，比如直接写`$3`；

##### 10.创建一个属于自己的专属设置项

`package.json`文件新增如下：

```
"contributes": {
    "configuration": {
        "type": "object",
        // 显示在配置页左侧
        "title": "hwl插件demo",
        "properties": {
            // 全局唯一的配置ID
            "vscodePluginDemo.yourName": {
                "type": "string",
                "default": "guest",
                "description": "你的名字"
            },
            "vscodePluginDemo.showTip": {
                "type": "boolean",
                "default": true,
                "description": "启动时显示自定义欢迎页"
            }
        }
    }
}
```

##### 11.针对上面的专属设置可以读取和修改设置

读取

```
// 如果没有设置，返回undefined
const result = vscode.workspace.getConfiguration().get('vscodePluginDemo.yourName ');
```

修改：

```
// 最后一个参数，为true时表示写入全局配置，为false或不传时则只写入工作区配置
vscode.workspace.getConfiguration().update('vscodePluginDemo.yourName', '前端艺术家', true);
```







### 五、 发布

```
$ npm install -g vsce 		// 安装打包工具
```

1. 更新修改packget.json

   > 发布人：  publisher
   >
   > 版本号： version



<img src="https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode04.png" width="50%" height="30%">

1. 更新README.md文件

2. 打包

   ```
   $ vsce package
   ```

3. 打包成功后会生成一个 `插件名-版本.vsix` 文件  例： `hwl-vscode01-0.0.1.vsix`

4. 在[插件市场官网](https://marketplace.visualstudio.com/manage/publishers/)上传插件

   ![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode05.png)

5. 选择.vsix文件上传

   ![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode06.png)

6. 发布成功

   ![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode07.png)

7. 可在vscode 插件中搜索到

   ![img](https://gitee.com/huangweiliang/picBed/raw/master/images/vscode/vscode08.png)









### 参考资料

1. [vscode 插件官网](https://marketplace.visualstudio.com/)
2. [vscode插件开发实践与demo源码](https://www.jianshu.com/p/673b0a114212)
3. [vscode api](https://code.visualstudio.com/api/references/vscode-api)
4. [VSCode插件开发全攻略概览](https://www.cnblogs.com/liuxianan/p/vscode-plugin-overview.html)

