# Github

1. Github 文档 首页底部 `https://docs.github.com/zh`
2. 使用emoji表情 :EMOJICODE: 直接得到真正的 emoji 表情 `https://docs.github.com/zh/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#using-emojis` `https://github.com/ikatyang/emoji-cheat-sheet/blob/master/README.md` 🙂

## 拉代码/推代码时 github.com 连接超时

ssh: connect to host github.com port 22: Connection timed out

**问题原因**

```
> ping github.com

正在 Ping github.com [20.205.243.166] 具有 32 字节的数据:
请求超时。
请求超时。
请求超时。

20.205.243.166 的 Ping 统计信息:
    数据包: 已发送 = 3，已接收 = 0，丢失 = 3 (100% 丢失)，
```

**解决办法**

最终要实际连接的是 ssh.github.com，  
这个二级域名能够正常访问，  
所以做一下本地域名重定向

```
> ping ssh.github.com

正在 Ping ssh.github.com [20.205.243.160] 具有 32 字节的数据:
来自 20.205.243.160 的回复: 字节=32 时间=90ms TTL=112
来自 20.205.243.160 的回复: 字节=32 时间=93ms TTL=112
来自 20.205.243.160 的回复: 字节=32 时间=93ms TTL=112

20.205.243.160 的 Ping 统计信息:
    数据包: 已发送 = 3，已接收 = 3，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 90ms，最长 = 93ms，平均 = 92ms
```

修改 ~/.ssh/config

```
Host github.com
  HostName ssh.github.com  # 这是最重要的部分
  User git
  Port 443
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa
```

## 在线修改文件名

访问文件 -> 点击右上角编辑按钮 -> 修改上方输入框里的文件名 -> 点击右上方提交修改 -> 完成
