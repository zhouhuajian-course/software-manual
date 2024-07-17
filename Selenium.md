# Selenium

## Selenium WebDriver

https://www.selenium.dev/

1. send_keys("admin") 如果原来 input 有 value，不会覆盖，而是追加，所以需要覆盖，需要清空一下，可以先调clear()，或者使用动作链进行双击选中文本，再输入就是覆盖，
    ```
    element = driver.find_element(By.NAME, "password")
    actions = ActionChains(driver)
    actions.double_click(element).perform()
    ```
2. PyCharm 调试时，使用新打开的Chrome，右上角操作，各占一半，更方便，而不是用PyCharm的右上角操作各占一半，会麻烦很多，当然，好像用PyCharm各占一半，也挺方便的，看习惯吧
3.  debug 调试完使用 继续 Resume Program 的方式，而不是停止，否者可能要按两次，才能停
4.  clear 无效时，使用driver.refresh()，或者js代码把value设置为空字符串

## Selenium IDE

https://www.selenium.dev/selenium-ide/

1. Selenium IDE 为每个与之交互的元素记录多个定位器。如果一个定位器在播放过程中失败，则会尝试其他定位器，直到其中一个成功；
2. 通过使用运行命令，您可以在一个测试用例中重复使用另一个测试用例（例如，允许您在整个套件中的多个位置重复使用登录逻辑）；
3. 不用先访问页面，开始录制时，会打开新窗口，访问页面
4. 先创建测试用例，然后点开始录制，每次开始录制都会打开新窗口
5. 只是个扩展 保存，其实就是下载，第一次右侧没有选项，这个是bug，保存项目，然后右上角重新打开项目即可，不需要重启Selenium IDE （下次留意，可能是左侧到了Executing界面才会这样，换成tests界面可能就好了）
6. 打开的窗口 Selenium IDE，他的图标都变成 Selenium 的图标，本质上其实就是一个，浏览器窗口，页面都是HTML CSS渲染的，交互则是 JavaScript，让你看起来像是个软件一样，其实不是只是个页面而已；
7. 下载的东西 .side .py 都好，默认下到下载目录，然后可以看Chrome下载记录，方便查看，方便打开；点击右侧 在文件夹显示，可方便拷贝整个文件
8. 测试模块名，可以以url命名 例如/login，就叫login /home就叫home /pms/product就叫pms_product
9. 上传文件 Chrome 才支持，value传文件路径，不需要两边的双引号，Windows复制路径时，两边会有双引号；
10. 但选择文件的窗口还是会弹出，原因是做了点击操作，解决办法是把上面点击的命令给删除掉即可， https://www.selenium.dev/selenium-ide/docs/en/api/commands#type
    ```
    type
    Sets the value of an input field, as though you typed it in. Can also be used to set the value of combo boxes, check boxes, etc. In these cases, value should be the value of the option selected, not the visible text. Chrome only: If a file path is given it will be uploaded to the input (for type=file), NOTE: XPath locators are not supported.
    ```
11. Selenium IDE 把命令清理干净后，再调试，再导出更好，右键可以删除
12. 创建 Test Suites ，类似文件夹，然后创建测试用例，然后再test suites里面添加创建出来的测试用例
13. 命令 wait for text Value 是文本内容，命令wait for element visible Value 是超时时间，单位毫秒
14. Test Suites 使用 login-page home-page 的命名方式，测试用例使用 login-success login-fail show-stats的命名方式
