---
title: 软件-搭建ngrok2服务器内网穿透
comments: true
toc: false
date: 2018-05-24 16:28:46
updated:
tags:
categories:
banner:
---
apt-get update
apt-get install git
cd /home/yangzheng
git clone https://github.com/inconshreveable/ngrok.git ngrok2 
网速不好的话可以先下载到电脑上再上传服务器
apt-get install golang #安装go语言
域名解析添加二级域名 ngrok.zyzypy.com  A ip地址
设置变量export NGROK_DOMAIN='ngrok.zyzypy.com 
生成证书
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$NGROK_DOMAIN" -days 5000 -out rootCA.pem
openssl genrsa -out server.key 2048
openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 5000

GOOS=darwin GOARCH=amd64 make release-client  # mac客户端  # 网速不好会断掉
GOOS=linux GOARCH=amd64 make release-server # ubuntu服务端 go env 查看一般amd64 386表示x86 32
启动服务端 
/home/yangzheng/ngrok2/bin/ngrokd -tlsKey=/home/yangzheng/ngrok2/server.key -tlsCrt=/home/yangzheng/ngrok2/server.crt -domain="ngrok.zyzypy.com" -httpAddr=":8080" -httpsAddr=":8081" -tunnelAddr=":4443" 
/home/yangzheng/ngrok2/bin/ngrokd  -domain="ngrok.zyzypy.com" -httpAddr=":8080" -httpsAddr=":8081" -tunnelAddr=":4443"
安装go1.4
配置环境变量
src下  GOOS=darwin GOARCH=amd64 ./make.bash
ngrok下 GOOS=darwin GOARCH=amd64 make release-client

