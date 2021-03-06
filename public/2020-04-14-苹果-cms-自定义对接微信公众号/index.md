# 苹果 cms 自定义对接微信公众号

## 苹果 cms 自定义对接微信公众号
### 概述
苹果 cms 自带的微信对接能实现`关注回复`、`少量关键字词回复`，
功能很鸡肋，对接并没有什么乱用。所以自己用就学习了下怎么自己对接，
>逻辑：用python的`requests库`调用苹果自己的开放接口，获得列表信息，用`webrobot`对接微信。

### 实现方式
1. 苹果 cms 后台开启开放 api，默认关闭的。
[![https://ae01.alicdn.com/kf/H9445613f6057452ca17022526abe8f799.png](https://ae01.alicdn.com/kf/H9445613f6057452ca17022526abe8f799.png "https://ae01.alicdn.com/kf/H9445613f6057452ca17022526abe8f799.png")](https://ae01.alicdn.com/kf/H9445613f6057452ca17022526abe8f799.png "https://ae01.alicdn.com/kf/H9445613f6057452ca17022526abe8f799.png")
2. 安装脚本用到的 python 库
```shell
#最新版本引入模块失败会，没有找到解决方法
pip install werobot=0.6.1
```
```shell
pip install requests
```

3. 创建 python 脚本，wx.py

```
#!/usr/bin/evn python
# coding=utf-8
import werobot,requests,json,sys
reload(sys)
sys.setdefaultencoding('utf8')
from werobot.reply import ArticlesReply, Article
robot = werobot.WeRoBot(token='robot')//自定义令牌，公众号会用到

# 被关注回复信息
@robot.subscribe
def subscribe(message):
    return '''欢迎关注YY影院
 1.输入要搜索的视频即可获得影片信息
 2.输入 `博客` 关键字可以获取我的博客地址
'''

# 关键字 博客 回复
@robot.filter('博客')
def echo(message):
    reply = ArticlesReply(message=message)
    article=Article(
        title="blog of Jw",
        description="Jw的博客",
        img="https://ae01.alicdn.com/kf/H06e4467cda7449bc84ffc8215e9bb0ca6.png",
        url="https://blog.gojw.xyz"
)
    reply.add_article(article)
    return reply

#影片查询
@robot.text
def findvedio(message,session):
    d = {'wd': message.content}
#后台开放api地址 
    results = requests.get('http://yyqqz.com/api.php/provide/vod/ac=list', params=d).json()
    return_value =''
    total=results['total']
    if total==0:
       return '没有搜索到您想要的信息，请私聊站长帮忙'
    for row in results['list']:
        Name = row['vod_name']
        burl='https://yyqqz.com/voddetail/'+str(row['vod_id'])
        return_value += '名称: %s\n' % Name
        return_value += '详情: %s\n' % burl

    return return_value
#收到图片的回复
@robot.image
def getImage(message):
    return'This is a picture!'
#配置端口3008,微信对接只支持80和443端口，所以需要反向代理下，看第4步
robot.config['HOST'] = '0.0.0.0'
robot.config['PORT'] = 3008
```
4. 直接在网站配置末尾添加
[![https://ae01.alicdn.com/kf/H6d35bd2b655a42df84d3a4a33e7a986dT.png](https://ae01.alicdn.com/kf/H6d35bd2b655a42df84d3a4a33e7a986dT.png "https://ae01.alicdn.com/kf/H6d35bd2b655a42df84d3a4a33e7a986dT.png")](https://ae01.alicdn.com/kf/H6d35bd2b655a42df84d3a4a33e7a986dT.png "https://ae01.alicdn.com/kf/H6d35bd2b655a42df84d3a4a33e7a986dT.png")

```json

upstream robot.yyqqz.com {
	server localhost:3008;
    }
 server {
	listen   80;
	server_name  robot.yyqqz.com;#所有访问子域名robot.yyqqz.com代理到 robot.yyqqz.com的upstream,也就是本地的3008端口
	location / {
		proxy_pass http://robot.yyqqz.com;
	}
    }
```

域名解析参考

[![https://ae01.alicdn.com/kf/H38c1ac05ab0045a381f101e0054d72ce6.png](https://ae01.alicdn.com/kf/H38c1ac05ab0045a381f101e0054d72ce6.png "https://ae01.alicdn.com/kf/H38c1ac05ab0045a381f101e0054d72ce6.png")](https://ae01.alicdn.com/kf/H38c1ac05ab0045a381f101e0054d72ce6.png "https://ae01.alicdn.com/kf/H38c1ac05ab0045a381f101e0054d72ce6.png")

5. 启动服务
```json
python wx.py
```
如果全都调试完毕，让脚本后台运行即可
```json
nohup python wx.py &
```
6. 微信公众号设置服务器地址和令牌
在开发-->基本配置 ，设置你的服务地址和令牌
[![https://ae01.alicdn.com/kf/He39e055ff25e4cff8a106a6785fe84baR.png](https://ae01.alicdn.com/kf/He39e055ff25e4cff8a106a6785fe84baR.png "https://ae01.alicdn.com/kf/He39e055ff25e4cff8a106a6785fe84baR.png")](https://ae01.alicdn.com/kf/He39e055ff25e4cff8a106a6785fe84baR.png "https://ae01.alicdn.com/kf/He39e055ff25e4cff8a106a6785fe84baR.png")
7. 效果

[![https://ae01.alicdn.com/kf/Hb21a95265bc64805afb24df2169e2558r.jpg](https://ae01.alicdn.com/kf/Hb21a95265bc64805afb24df2169e2558r.jpg "https://ae01.alicdn.com/kf/Hb21a95265bc64805afb24df2169e2558r.jpg")](https://ae01.alicdn.com/kf/Hb21a95265bc64805afb24df2169e2558r.jpg "https://ae01.alicdn.com/kf/Hb21a95265bc64805afb24df2169e2558r.jpg")

### 参考
[[1]werobot中文文档][werobot中文文档]

[werobot中文文档]: https://werobot.readthedocs.io/zh_CN/latest/index.html "werobot中文文档"


