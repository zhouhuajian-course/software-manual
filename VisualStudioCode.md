# VisualStudioCode

https://github.com/Microsoft/vscode/ 

主要由 TypeScript 开发

TypeScript 是微软开发的一个开源的编程语言，通过在 JavaScript 的基础上添加静态类型定义构建而成。

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
