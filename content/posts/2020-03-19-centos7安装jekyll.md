---
category: linux
layout: post
tags:
  - centos
  - jekyll
  - github
title: centos7安装jekyll(自动拉取GitHub提交)
date: 2019-03-19
---
* TOC
{:toc}

## rvm安装jekyll安装
> githubpages 运行的jekyll响应太慢，于是决定放到主机上，并实现提交到github的文件，主机自动拉取,jekyll自动渲染。

>利用 docker-compose 快速安装 nginx，提供https支持，申请证书可以参考[这篇](https://blog.csdn.net/qq_39846820/article/details/103496925)，免费证书，自动续签。

## 流程图

<img src="https://raw.githubusercontent.com/jw-star/myFigurebed/master/img/20200319184023.svg?sanitize=true" width="400px">

## jekyll

#### ruby环境
```java
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
```

```powershell
 curl -sSL https://get.rvm.io | bash -s stable
```

```powershell
source /etc/profile.d/rvm.sh
```
#### 安装 ruby 2.4.2
```powershell
rvm install 2.4.2
```
#### jekyll环境

```powershell
yum install ruby-full build-essential zlib1g-dev
```
以下命令会将环境变量添加到〜/ .bashrc文件中，以配置gem安装路径
```powershell
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
#### jekyll安装

```powershell
gem install jekyll bundler
```
可能遇到 RubyGems 版本太低，按提示升级，重新安装 jekyll
漫长等待。。。jekyll安装好了就
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2p3LXN0YXIvbXlGaWd1cmViZWQvbWFzdGVyL2ltZy8yMDIwMDMxOTAxMDQ1NC5wbmc?x-oss-process=image/format,png)

也可能提示 rubygem版本低，升级就可以了
```html
gem install rubygems-update
update_rubygems
gem update --system
```
---
---
## 自动拉取相关
原理就是 push 到 github 仓库后，github回调一个地址，本地用node.js接收回调，执行脚本，拉取仓库并让jekyll重新渲染，也可以设置增量渲染
#### centos7.7安装node

```powershell
rpm -ivh https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-11.noarch.rpm
```

```powershell
yum install -y nodejs
```
安装 github-webhook-handler ，主要靠它webhook

```powershell
npm install -g github-webhook-handler     #安装 github-webhook-handler
```
进入目录，脚本再这里，可以直接引入到js文件中，
```powershell
cd  /usr/lib/node_modules/github-webhook-handler
```
js文件,需要修改的文件已经注释
参考图片设置 webhook
![webhook 设置](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2p3LXN0YXIvbXlGaWd1cmViZWQvbWFzdGVyL2ltZy8yMDIwMDMxOTEzMjUzNy5wbmc?x-oss-process=image/format,png)
```powershell
var http = require('http')
var createHandler = require('github-webhook-handler')
var handler = createHandler({ path: '/deploy', secret: 'password' }) //监听请求路径，和Github 配置的密码
 
function run_cmd(cmd, args, callback) {
  var spawn = require('child_process').spawn;
  var child = spawn(cmd, args);
  var resp = "";
 
  child.stdout.on('data', function(buffer) { resp += buffer.toString(); });
  child.stdout.on('end', function() { callback (resp) });
}
 
http.createServer(function (req, res) {
  handler(req, res, function (err) {
    res.statusCode = 404
    res.end('no such location')
  })
}).listen(3006)//监听的端口
 
handler.on('error', function (err) {
  console.error('Error:', err.message)
})
 
handler.on('push', function (event) {
  console.log('Received a push event for %s to %s',
    event.payload.repository.name,
    event.payload.ref);
  run_cmd('sh', ['/usr/local/depoly.sh'], function(text){ console.log(text) });//成功后，执行的脚本。
})
```
shell脚本 deploy.sh

```powershell
#!/bin/bash
gitname=jw-star #修改为你的仓库
cd /blog/${gitname}.github.io
echo start pull from github 
git pull http://github.com/${gitname}/${gitname}.github.io.git
echo start build..
jekyll build --destination=/blog/site #设置渲染到哪个目录
```
为了让 deploy.js 持续运行 ，安装 forever 守护进程，

```powershell
npm install forever -g   #安装
```
常见参数
	-a, –append: 合并日志
	-l LOGFILE: 输出日志到LOGFILE
	forever list : 查看日志位置，可以根据日志判断脚本执行情况

启动脚本
```powershell
forever start -a -l  forever.log   deploy.js
```
**成功的话在 site 目录下会有渲染好的文件**
#### Nginx相关
docker 安装参考 [这篇](https://blog.csdn.net/qq_39846820/article/details/103687861)
docker-compose 参考 [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)
先贴一下整体目录结构

```powershell
tree -L 2 /blog/          #- L 2子目录显示到两层
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2p3LXN0YXIvbXlGaWd1cmViZWQvbWFzdGVyL2ltZy8yMDIwMDMxOTEzMzkwMC5wbmc?x-oss-process=image/format,png)
docker-compose.yml 

```yaml
version: '3'
services:
    nginx:
      container_name: nginx
# build:
#   context: .
#   dockerfile:  ./nginx/Dockerfile
      image: "nginx:latest"
      restart: always #出现错误自动重启
      volumes:
        - ./site:/usr/share/nginx/html #站点目录映射到容器的目录
        - ./nginx/conf.d/:/etc/nginx/conf.d #映射配置文件到容器目录
      ports:
        - 443:443

```
default.conf

```powershell
server {
    listen 443 ssl;
    server_name  localhost;
    ssl on; #强制https,这是趋势

    ssl_certificate /etc/nginx/conf.d/fullchain.cer;
    ssl_certificate_key /etc/nginx/conf.d/gojw.xyz.key;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

     # 指定密码为openssl支持的格式
     ssl_protocols  SSLv2 SSLv3 TLSv1.2;

     ssl_ciphers  HIGH:!aNULL:!MD5;  # 密码加密方式
     ssl_prefer_server_ciphers  on;   # 依赖SSLv3和TLSv1协议的服务器密码将优先于客户端密码
     location / {
        root  /usr/share/nginx/html;
        index  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}

```
#### 启动服务

```c
docker-compose up -d         #-d 后台启动
```

