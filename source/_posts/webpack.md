---
title: webpack使用笔记
categories:
  - 笔记
tags:
  - 前端
  - Javascript
  - webpack
  - 笔记
date: 2019-12-19 00:12:05
---

从17年应届进入一家外包开始到现在已经呆过三家公司了，从前后端分离的情况来看，做的最好竟然还是那家外包公司，虽然他们用着eclipse进行编写页面调试，用着velocity模板引擎，但至少给他们数据结构，他们还是能把页面给做好。后来去了家创业公司，前端也只是制作静态页面，当给数据时，还得我们自己来调整页面，实在调不准才请教前端人员，可惜公司资金链断了。来到第三家公司，或许因为业务形式的原因，这一家公司完全没有专门的前端，后端的人揽了全部的活，唯一的优点就是有了专门的运维，我也不至于再去折腾服务器。  
面试过很多人，绝大部分都是只专注于后端，没接触过前端，也有几个写过页面，但是对于js却一窍不通，零星的几个全栈工程师，也不知道什么原因被领导拒绝了。于是乎就有了将公司业务进行前后端分离的计划。<!-- more -->  
自己虽然说也经常写页面，但是都是那种右键notepad++打开编辑的那种，没有用过什么工具之类的，只是在学习vue的偶然机会，接触到了webpack，感受到它的好处，才决定开始用它。  
网上找过很多博客，发现很多都已经不准确了，所以记录下笔记。
# 本机环境及开发工具
- node.js v10.16.3
- gitBash
- vscode

# 开始
## 初始化项目
创建一个空文件夹，然后进入并打开Git Bash。
输入`npm init`。会出现如下提示，不用在意，狂按回车即刻。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/1.png)  
之后会生成一个package.json的文件，类似于java中maven的pom.xml，进行依赖包管理。
## 安装cnpm
Git Bash中输入`npm install -g cnpm --registry=https://registry.npm.taobao.org`即可安装cnpm，如若已安装，请忽略。
## 安装webpack
在Git Bash中输入
```
cnpm install webpack --global  
cnpm install webpack webpack-cli --save-dev
```
其中`--global`表示全局安装，可用`-g`代替，`--save-dev`表示本地项目安装，可用`-D`代替，
`install`就是字面意思安装，可用`i`代替。
之前查阅博客的时候，大部分都是用缩写的，也没有说明其意思，一直没搞懂，所以这里记录一下，有些博客安装是`cnpm install --global webpack`，对其顺序感到疑惑，但经过测试都是一样的效果。
这时候会多出一个node_modules文件夹，里面都是用到的js，当做java中的jar包。
## 一个简单示例
创建文件夹dist与src，并在dist中创建index.html，在src中创建index.js与test.js。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/2.png)
``` html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<body>
    <div id='test'></div>
</body>
<script src="bundle.js"></script>
</html>
```
``` js
//test.js
module.exports = function () {
    var div = document.createElement('div');
    div.innerHTML = "Hello World!";
    return div;
};
```
``` js
//index.js
const test = require('./test');
document.getElementById("test").appendChild(test());
```
在Git Bash中输入`webpack src/index.js --output dist/bundle.js`，意思是将index.js打包成bundle.js。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/3.png)
此时dist目录下就生成了bundle.js，接着打开index.html。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/4.png)。
这样就成功了。
## webpack.config.js 配置简化打包
在根目录创建webpack.config.js。
``` js
//webpack.config.js
const path = require('path');

module.exports = {
    entry: path.join(__dirname, "/src/index.js"),
    output: {
        path: path.join(__dirname, "/dist"),
        filename: "bundle.js"
    }
}
```
`require`与`module.exports`是CommonJS模块规范内容。  
`entry`为入口文件，就是将这个文件打包。  
`output.path`为打包后存放地址。  
`output.filename`为打包后文件名。  
`path.join()`是node.js的path模块功能，也可以写成`entry: __dirname+"/src/index.js"`，看个人喜好。  
`__dirname`是node.js的全局变量，表示脚本执行所在的根目录。  
再在Git Bash中输入`webpack`，就会生成bundle.js。webpack会去读取webpack.config.js中的配置选项。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/5.png)
## 进一步优化
修改package.json中的"scritp"。
```
{
  "name": "webpackblog",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "webpack"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.41.3",
    "webpack-cli": "^3.3.10"
  }
}
```
再在Git Bash中输入`npm run build`，bundle.js会再次生成。如果package.json中的"scripts"的build修改为start，则直接执行`npm start`就可以了，因为start比较特殊，只有它才能省略run。  
之所以用这样的方式是因为项目往往是有多个环境的，每个环境的配置不一样，就需要执行不同的命令，主要还是让开发人员专心开发，不需要关注配置什么之类的，构建项目时就是傻瓜式执行就可以了。
# 使用本地服务器
## 安装webpack-dev-server
Git Bash中输入`cnpm install webpack-dev-server --save-dev`。
## 修改配置
修改webpack.config.js
``` js
const path = require('path');

module.exports = {
    entry: path.join(__dirname, "/src/index.js"),
    output: {
        path: path.join(__dirname, "/dist"),
        filename: "bundle.js"
    },
    devServer: {
        contentBase: "./dist",
        port: "8080",  
        inline: true, 
        historyApiFallback: true, 
    }
}
```
devServer的参数说明可以去[官方](https://www.webpackjs.com/configuration/dev-server/)查看，个人感觉官方说明已经足够了。  
新增package.json中的"script"。
```
"scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server --open"
}
```
Git Bash中输入`npm run dev`即可启动服务，因为加了`--open`，所有启动会会自动打开浏览器。
在Git Bash 输入`Ctrl+C`即可退出服务，好像很多东西都是`Ctrl+C`退出的。
## 代码调试
我们现在生成的bundle.js其实是不能调试的，打开页面之后找不到原来写的js，所以有了`source-map`。  
编辑webapck.config.js。
``` js
const path = require('path');

module.exports = {
    entry: path.join(__dirname, "/src/index.js"),
    output: {
        path: path.join(__dirname, "/dist"),
        filename: "bundle.js"
    },
    devServer: {
        contentBase: "./dist",
        port: "8080",  
        inline: true, 
        historyApiFallback: true, 
    },
    devtool: 'source-map'
}
```
此时在执行`npm run build`，会多生成一个bundle.js.map，再打开index.hmtl，按F12查看Sources，
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/6.png)
源码都可以查看了，并且可以进行打断点调试。  
这里一个忠告：虽然说这样可以调试了，但是进入断点时，你所看到参数具体值可能不是实际值，有问题的地方建议用console.log()打印一下比较好。
# Loaders
以我现在用到的loaders来讲，最牛逼的就是可以将ES6等新语法转化成ES5以及以下的语法，让浏览器能够识别。
## Babel
Babel就是将ES6语法编译成让浏览器能够识别。
Git Bash输入`cnpm install babel-core babel-loader babel-preset-env --save-dev`进行安装。
修改webpack.config.js
``` js
const path = require('path');

module.exports = {
    entry: path.join(__dirname, "/src/index.js"),
    output: {
        path: path.join(__dirname, "/dist"),
        filename: "bundle.js"
    },
    devServer: {
        contentBase: "./dist",
        port: "8080",  
        inline: true, 
        historyApiFallback: true, 
    },
    devtool: 'source-map',
    module:{
        rules:[
            {
                test: /\.js$/,
                use:{
                    loader:"babel-loader",
                    options:{
                        presets:[
                            "env"
                        ]
                    }
                },
                exclude:/node_modules/
            }
        ]
    },
}
```
按道理，此时执行`npm run build`应该就可以了，但实际却报了错，按照百度来说是`babel-loader`与`babel`要求版本一致，所以输入`cnpm install babel-loader@7 babel-core babel-preset-env --save-dev` 让其版本统一。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/7.png)
版本统一指挥在执行`npm run build`，就没问题了。  
最简单的验证方法，在test.js中使用箭头函数，
``` js
module.exports = function () {
    var div = document.createElement('div');
    div.innerHTML = "Hello World!";
    return div;
};

var a=()=>{
    console.log("这只是用来测试功能");
}

new a();
```
执行`run npm build`之后在bundle.js中`Ctrl+F`寻找“这只是用来测试功能”，发现箭头函数没了。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/8.png)
# 插件
## HtmlWebpackPlugin自动生成html文件
安装命令`cnpm install html-webpack-plugin --save-dev`  
在src文件夹下创建template.html。
``` html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>Test</title>
</head>
<body>
    <div id='test'></div>
</body>
</html>
```
编辑webpack.config.js
``` js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: path.join(__dirname, "/src/index.js"),
    output: {
        path: path.join(__dirname, "/dist"),
        filename: "bundle.js"
    },
    devServer: {
        contentBase: "./dist",
        port: "8080",  
        inline: true, 
        historyApiFallback: true, 
    },
    devtool: 'source-map',
    module:{
        rules:[
            {
                test: /\.js$/,
                use:{
                    loader:"babel-loader",
                    options:{
                        presets:[
                            "env"
                        ]
                    }
                },
                exclude:/node_modules/
            }
        ]
    },
    plugins:[
        new HtmlWebpackPlugin({
            template:path.join(__dirname,"/src/template.html")
        }),
    ]
}
```
删除dist文件夹，然后执行`npm run build`，会发现页面也生成了。
## HotModuleReplacementPlugin热更新
可以在修改代码之后自动刷新页面。
修改webpack.config.js即可使用。
``` js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack=require('webpack');

module.exports = {
    entry: path.join(__dirname, "/src/index.js"),
    output: {
        path: path.join(__dirname, "/dist"),
        filename: "bundle.js"
    },
    devServer: {
        contentBase: "./dist",
        port: "8080",  
        inline: true, 
        historyApiFallback: true, 
        hot: true 
    },
    devtool: 'source-map',
    module:{
        rules:[
            {
                test: /\.js$/,
                use:{
                    loader:"babel-loader",
                    options:{
                        presets:[
                            "env"
                        ]
                    }
                },
                exclude:/node_modules/
            }
        ]
    },
    plugins:[
        new HtmlWebpackPlugin({
            template:path.join(__dirname,"/src/template.html")
        }),
        new webpack.HotModuleReplacementPlugin(),
    ]
}
```
## CleanWebpackPlugin清理文件
主要功能是构建项目的时候先清理掉dist中的文件，防止dist中文件变得杂乱。  
安装命令`cnpm install clean-webpack-plugin --save-dev`
webpck.config.js文件。
``` js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack=require('webpack');
const {CleanWebpackPlugin} = require('clean-webpack-plugin');

.
. 省略
.
    plugins:[
        new HtmlWebpackPlugin({
            template:path.join(__dirname,"/src/template.html")
        }),
        new webpack.HotModuleReplacementPlugin(),
        new CleanWebpackPlugin()
    ]
}
```
这里很多博客还是老版本的，使用`const CleanWebpackPlugin = require('clean-webpack-plugin');`和`new CleanWebpackPlugin(['dist'])`，在CleanWebpackPlugin新的版本中已经无效了，会报错。  
我没有具体看文档，但根据实际使用来看，会清理`output.path`下的所有文件。
# 其他
## 多入口
多入口很简单，只需要修改`entry`。
``` js
entry: {
    two: path.join(__dirname, "/src/index.js"),
    index: path.join(__dirname, "/src/index.js"),
},
output: {
    path: path.join(__dirname, "/dist"),
    filename: "[name].js"
},
```
再次执行`npm run build`，就会多生成一个文件，然后也会发现这两个文件名变成了two.js跟index.js了，因为这里的输出名更改为[name]，这会根据你所写的入口名来生成对应的文件名。  
弄了多入口，也就要弄多模板多生成页面，这很简单，只需要在webpack.config.js中多创建几个HtmlWebpackPlugin，并增加几个参数。
``` js
plugins: [
    new HtmlWebpackPlugin({
        filename: path.join(__dirname,"dist/index.html"),
        template: path.join(__dirname, "/src/template.html"),
        chunks: ['index']
    }),
    new HtmlWebpackPlugin({
        filename: path.join(__dirname,"dist/two.html"),
        template: path.join(__dirname, "/src/template.html"),
        chunks: ['two']   
    }),
    new webpack.HotModuleReplacementPlugin(),
    new CleanWebpackPlugin()
    ]
```
执行`npm run build`会生成index.html与two.html。  
filename就是生成文件路径名字，大家都能看懂。chunks对应的你webpack的入口名，生成的页面会引入相应的js。如果有多个模板页面也就只需要修改对应的template。
## 遇到的问题
1.关于css，webpack可以使用`style-loader`，`css-loader`，但由于公司业务是机顶盒上的，最开始写了demo并试了几款机顶盒，发现这样样式加载不到就没有再研究了。  
2.修改过output.path，使其js都生成在指定一个文件夹，
``` js
output: {
    path: path.join(__dirname, "/dist/js"),
    filename: "[name].js"
},
```
运行服务打开页面发现js没有加载到，经过百度发现是服务都把生成的js放在根目录（我是这么理解的），在浏览器输入http://localhost:8080/index.js ，果然有文件。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/9.png)
可是我们生成的页面里引入的js路径为js/index.js，修改这个路径总归不太好，所以需要修改webpack-dev-server的配置。
``` js
devServer: {
    contentBase: "./dist",
    port: "8080",
    inline: true,
    historyApiFallback: true,
    hot: true,
    publicPath:'/js/'
}
```
新增加的publicPath表示生成的js路径会多增加这个目录，这时候重新运行服务就会发现js可以正常引入到。
3.在页面里添加img标签，并引入本项目图片，运行服务，发现图片也跟之前的js一样加载不到，试过`url-loader`与`file-loader`，但研究发现这个对页面上的img标签是无效的，想了两个办法，第一个所有图片使用网络路径，这样就不存在本地图片加载不到，但是开发的时候略嫌麻烦。第二个是在dist创建相同的图片路径，比如:
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/10.png)
运行服务器，输入http://localhost:8080/img/1.jpg 是可以出现图片的，只要这个路径跟页面里的路径对应上就可以了，只不过是一个图片要拖到两个文件夹里，但是比第一个方法好点。
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/webpack%E7%AC%94%E8%AE%B0/11.png)