# MinIO

https://min.io/

## 安装启动

https://min.io/download?license=agpl&platform=windows

https://dl.min.io/server/minio/release/windows-amd64/minio.exe

放到 D:\MinIO

D:\MinIO\minio.exe server D:\MinIO\data --console-address ":9001"

## 不能把 MinIO 的数据目录放到 git

会不停有一堆临时文件在变动
