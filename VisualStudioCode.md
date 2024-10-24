# Visual Studio Code

https://github.com/Microsoft/vscode/ 

https://code.visualstudio.com/Docs

主要由 TypeScript 开发

TypeScript 是微软开发的一个开源的编程语言，通过在 JavaScript 的基础上添加静态类型定义构建而成。

## VS Code 学习

1. VS Code 是轻量级编辑器，大部分功能需要通过扩展的方式进行扩展；
2. VS Code 内置了对 JavaScript、TypeScript、HTML、CSS 等语言的支持；
3. 安装语言扩展可以添加对不同语言的支持，例如Python；
4. Tab 插入选定的建议，在 VS Code 中通常都是使用 Tab 来选中代码建议，而不是回车，这个很重要！！！；
5. Git U表示未跟踪/新文件；
6. VS Code 内置了对运行和调试 Node.js 应用程序的支持；

## 关闭鼠标悬停提示

设置 -> 搜 hover -> Editor › Hover: Enabled -> 取消勾选

## 扩展 extension Live Server

Live 形容词， /laɪv/

页面刷新策略，当VSCode保存文件时，刷新，按 Ctrl+S 会马上刷新，如果VSCode设置了每隔一秒自动保存，那么就是每隔一秒有保存就会自动刷新

## HTML CSS JS 代码缩进使用 2个空格 而不是 4个空格

设置 搜索 tab size ，设置为2，搜索 Detect Indentation，设置为 off，既取消勾选

## HTML 开发

1. 输入 ! 回车，生成 HTML 骨架
2. 输入 标签名 回车，生成 HTML 标签，比<标签名 要方便，语法类似CSS选择器，div#app，div.app
3. live server 代码修改，自动刷新页面，live server 以项目目录作为网站的根目录，访问 http://127.0.0.1:5500/ 可以看到效果
4. link:css 写 link 标签，引入 CSS 文件

## 实时预览 HTML

安装插件 Live Server 

必须打开项目 Open Folder，不能打开单独html使用

右键 Open With Live Server

## IntelliSense

智能感知

## IntelliCode 扩展

智能代码

Python, TypeScript/JavaScript and Java

## 打开 Linux 机器上的远程项目(目录)

扩展 -> 安装 Remote SSH -> Ctrl+Shift+P -> Remote-SSH: Connect to Host... -> root@192.168.1.206 -> 密码 root

File -> Open Folder -> /path/to/the/project or /path/to/the/directory
