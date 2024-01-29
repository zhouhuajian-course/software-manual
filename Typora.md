# Typora

## 修改 导出的 PDF 的字体大小

另一种办法 

样式选 Vue

1. 导出 HTML 带样式；
2. 编辑 样式 修改里面的字体大小 为 12px，一般修改一两处即可；
3. Chrome打开html；
4. 打印 选PDF；
5. 更多设置 去掉页眉页脚 边距选自定义 根据预览情况 调整；
6. 打印。

备注：不建议修改主题颜色全部为黑色，建议使用原有主题颜色，即使黑白打印出来变成不同的灰色

## 设置 导出的 PDF 的字体大小 (貌似无效)

偏好设置-》外观-》打开主题文件夹

修改对应主题CSS文件的里面的字体大小，貌似修改@media print的无效

```css
html {
    font-size: 12px;
}
```

## 设置 Pandoc 路径

官网下载 zip

https://pandoc.org/installing.html 

解压

设置 pandoc.exe 的位置
