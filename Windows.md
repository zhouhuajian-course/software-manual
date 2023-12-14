# Windows

## 缩放后，个别软件界面模糊

右键 -> 属性 -> 更改高DPI设置 ->  
替代高DPI缩放行为 -> 勾选 -> 确定 -> 确定

## Firefox 手动扩展安装

```
解决 安装 AdBlock 出现 您尝试访问的页面在您的地区不可用。

1. 访问 https://addons.mozilla.org/zh-CN/firefox/
2. 搜索AdBlock
3. 下载扩展 adblock_for_firefox-5.4.2.xpi
4. 拖到 Firefox 即可安装
(xpi文件也可以通过其他网站下载)
```

## 内网远程桌面控制

```
@echo off & title 更新RDPWrap.ini
 
set INI_Path="C:\Program Files\RDP Wrapper\rdpwrap.ini"
set INI_Dir="C:\Program Files\RDP Wrapper"
 
::检查权限
setlocal enabledelayedexpansion>nul
net session>nul
if !ERRORLEVEL! EQU 2 (
	set "args=!args: ="^&chr^(32^)^&"%!"
	
	set "args="/C"&chr(32)&chr(34)&chr(94)&chr(34)&"%~f0""
	mshta "vbscript:CreateObject("Shell.Application").ShellExecute("cmd.exe", !args!, NULL, "runas", NULL)(window.close)"&&exit
)
 
echo.正在停止远程桌面服务……
echo Y | net stop UmRdpService
echo Y | net stop TermService
 
::删除旧配置文件
:DeleteFile
del %INI_Path%
if exist %INI_Path% (
	echo.文件 %INI_Path% 仍被占用，请手动关闭占用该文件的程序。
	start "" %INI_Dir%
	pause
	goto :DeleteFile
)
 
echo.正在下载配置文件……
curl "https://raw.githubusercontents.com/sebaxakerhtc/rdpwrap.ini/master/rdpwrap.ini">%INI_Path%
 
echo.正在重启远程桌面服务……
C:\WINDOWS\System32\svchost.exe -k NetworkService
net start TermService
 
echo.更新完成！按任意键以结束。
pause>nul
```
