# Visual Studio Code

https://github.com/Microsoft/vscode/ 

https://code.visualstudio.com/Docs

主要由 TypeScript 开发

TypeScript 是微软开发的一个开源的编程语言，通过在 JavaScript 的基础上添加静态类型定义构建而成。

## VS Code 学习

todo: 上次看到这里 https://code.visualstudio.com/docs/editor/extension-marketplace

1. VS Code 是轻量级编辑器，大部分功能需要通过扩展的方式进行扩展；
2. VS Code 内置了对 JavaScript、TypeScript、HTML、CSS 等语言的支持；  
   Visual Studio Code 包括内置的 JavaScript IntelliSense、调试、格式化、代码导航、重构和许多其他高级语言功能。  
   Visual Studio Code 开箱即用地支持 JavaScript 和 TypeScript 语言以及 Node.js 调试。但是，要运行 Node.js 应用程序，您需要在计算机上安装 Node.js 运行时。  
   
3. 安装语言扩展可以添加对不同语言的支持，例如Python；支持数百种编程语言；
4. Tab 插入选定的建议，在 VS Code 中通常都是使用 Tab 来选中代码建议，而不是回车，这个很重要！！！；
5. Git U表示未跟踪/新文件；
6. VS Code 内置了对运行和调试 Node.js 应用程序的支持；
7. 快速搜索和打开文件。键盘快捷键：Ctrl+P；提示：输入?可查看命令建议。
8. 快速搜索和执行命令。键盘快捷键：Ctrl+Shift+P；也可以 Ctrl+P后按>
9. 打开最近的文件夹或工作区。键盘快捷键：Ctrl+R；
10. 扩展 键盘快捷键：Ctrl+Shift+X
11. emmet /ˈem.ɪt/((蚂蚁) 内置扩展，核心功能是 通过代码缩写 生成代码
12. 用户 settings.json 位置
```text
根据您的平台，用户设置文件位于此处：

视窗 %APPDATA%\Code\User\settings.json
macOS $HOME/Library/Application\ Support/Code/User/settings.json
Linux $HOME/.config/Code/User/settings.json
```
13. 通过 Live Share 进行协作。Live Share使您能够与朋友、同学或教授快速实时协作处理同一代码，而无需同步代码或配置相同的开发工具、设置或环境。
14. 跨文件搜索。VS Code 允许您快速搜索当前打开的文件夹中的所有文件。按Ctrl+Shift+F并输入您的搜索词。搜索结果按包含搜索词的文件分组，并指示每个文件中的匹配项及其位置。展开文件以查看该文件中所有匹配项的预览。然后单击其中一个匹配项以在编辑器中查看它。
15. 搜索和替换。您还可以跨文件进行搜索和替换。展开“搜索”小部件以显示“替换”文本框。
16. Visual Studio Code includes TypeScript language support but does not include the TypeScript compiler, tsc. You will need to install the TypeScript compiler either globally or in your workspace to transpile TypeScript source code to JavaScript (tsc HelloWorld.ts). `https://code.visualstudio.com/docs/languages/typescript` VS Code 支持 TypeScript 语言，但不包括 TypeScript 编译器，需要安装 TypeScript compiler `tsc` 。编译后，会删除类型信息。
```text
安装 TypeScript 最简单的方法是通过 npm（Node.js 包管理器）。如果你已经安装了 npm，你可以-g通过以下方式在计算机上全局安装 TypeScript（）：
npm install -g typescript
复制
您可以通过检查版本来测试您的安装。

tsc --version
复制
另一种选择是在您的项目中本地安装 TypeScript 编译器（npm install --save-dev typescript），其好处是可以避免与您可能拥有的其他 TypeScript 项目发生交互。
```
17. node命令，需要自己安装，VSCode不会自带；

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
