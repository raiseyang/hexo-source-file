title: curl操作手册
author: raiseyang
tags: []
categories:
  - 效率工具
date: 2017-11-12 13:23:00
---
# curl操作手册


---

- 官方网站:https://curl.haxx.se
- 下载地址:https://curl.haxx.se/download.html

## 安装步骤(windows)
1. 下载curl对应安装包`curl-7.56.1-win64-mingw.zip`
2. 解压文件到指定目录
3. 将`~/curl-7.56.1-win64-mingw/bin`设为环境变量
4. 打开命令行工具输入`curl http://www.baidu.com/`,若有返回,证明安装成功

<!-- more -->
## 简单使用
|参数|说明|
|--|--|
|-v|输出请求响应信息|
|-i|仅输出响应信息|
|--trace|保存打印日志|
|-o|下载文件|
|-H|设置请求头|
|-d|POST设置body|
|-F|上传文件|
|-r|设置请求范围|
|-u|用户名密码|
|-x|设置代理|

```
# 请求网站
curl http://www.baidu.com/
# 输出请求信息 -v
curl http://www.baidu.com/ -v
# 保存打印日志 --trace
curl http://www.baidu.com/ --trace trace.txt
# 下载文件 -o
curl http://www.baidu.com/ -o a.html
# 设置代理 -x
curl -x 61.152.230.26:8080 http://www.baidu.com/ -v
# 设置代理 用户名密码 -u
curl -u user:passwd -x my-proxy:888 http://www.get.this/
# 设置请求范围 -r
curl http://www.baidu.com/ -v -r 0-99 # 前0-99个字节
curl http://www.baidu.com/ -v -r -99 # 最后99个字节
# 上传文件 -F
curl http://127.0.0.1:5000/ -F "file=@D:\flicker.png" -X POST
# POST设置body -d
curl http://www.baidu.com -d 'wd=hello&ie=utf-8'
# 设置请求头 -H 
curl -H "X-you-and-me: yes" www.love.com
# 设置https证书 -E 只支持pem
curl -E /path/to/cert.pem:password https://secure.site.com/

```