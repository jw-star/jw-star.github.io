# docker-compose 安装 酷q机器人

## docker-compose 安装 酷q机器人

### 概述
之前用 werobot 对接了微信公众号，但是微信公众号限制太大，所以看了下怎么用qq机器人解决问题

### 安装
#### 目录结构

[![https://ae01.alicdn.com/kf/H1b4e29a4e21a44b5ab1fb188b618215d0.png](https://ae01.alicdn.com/kf/H1b4e29a4e21a44b5ab1fb188b618215d0.png "https://ae01.alicdn.com/kf/H1b4e29a4e21a44b5ab1fb188b618215d0.png")](https://ae01.alicdn.com/kf/H1b4e29a4e21a44b5ab1fb188b618215d0.png "https://ae01.alicdn.com/kf/H1b4e29a4e21a44b5ab1fb188b618215d0.png")

#### docker-compose.yml
```yaml
version: "3"
services:

  cqhttp:
    image: richardchien/cqhttp:latest
    volumes:
      - "./coolq:/home/user/coolq" # 用于保存COOLQ文件的目录
    environment:
      - COOLQ_ACCOUNT=169324812 # 指定要登陆的QQ号，用于自动登录
      - FORCE_ENV=true
      - CQHTTP_USE_HTTP=false
      - CQHTTP_USE_WS=false
      - CQHTTP_USE_WS_REVERSE=true
      - CQHTTP_WS_REVERSE_API_URL=ws://nonebot:8080/ws/api/
      - CQHTTP_WS_REVERSE_EVENT_URL=ws://nonebot:8080/ws/event/
    depends_on:
      - nonebot
    ports:
      - 9000:9000 

  nonebot:
    build: ./nonebot # 构建nonebot执行环境，Dockerfile见下面的例子
    expose:
      - "8080"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - "./qbot:/root/qbot" # 项目文件所在目录
    command: python3 /root/qbot/bot.py

```

#### Dockerfile

```yaml
FROM alpine
RUN apk add --no-cache tzdata python3 py3-multidict py3-yarl && \
        pip3 install --upgrade pip && \
        pip3 install --no-cache-dir "nonebot[scheduler]" && \
	pip3 install requests 

```
#### qbot文件夹上传编写好的代码

#### 运行
    docker-compose up --build
    docker-compose up --build -d //后台运行

#### 浏览器访问 ip:9000
输入密码，默认：MAX8char ，密码参考于：[密码参考](https://cqhttp.cc/docs/4.14/#/ "密码参考")
### 参考文章

1. [NoneBot:NoneBot 是一个基于 酷Q 的 Python 异步 QQ 机器人框架](https://nonebot.cqp.moe/guide/ "NoneBot:NoneBot 是一个基于 酷Q 的 Python 异步 QQ 机器人框架")
2. [docker-compose部署参考](https://nonebot.cqp.moe/advanced/deployment.html "docker-compose部署参考")








