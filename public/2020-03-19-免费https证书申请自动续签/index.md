# 免费https证书申请(自动续签)

* TOC
{:toc}
## 概述
这篇文章主要讲如何为你的服务器快速申请 https 证书
## https证书的作用 


1 **加密客户和服务之间的信息**  


- 如果通过http协议访问网站，浏览器和服务器之间是明文传输，账号、密码等机密信息随时可能被泄露 、窃取、篡改。

- 部署https加密证书后，使用HTTPS加密协议访问网站，可激活客户端浏览器到网站服务器之前的『SSL加密通道』（SSL协议），实现高强度双向加密传输，防止传输数据泄露或篡改。  

2 **利于网站 SEO 优化，提高网页排名**

* 部署了SSL证书的网站相比没有部署SSL证书的网站更加可信，更加安全，可以有效的保障用户的利益不受侵害。

* 因此搜索引擎如谷歌，百度在确保用户信息安全的角度，都在大力倡导网站部署SSL证书实现HTTPS加密访问。在搜索、展现、排序方面也给予部署了SSL证书网站的优待。  
## 利用acme.sh快速申请 https 证书 
#### 在~目录下，没有提示不要切换
```shell
curl https://get.acme.sh | sh
或者
wget -O -  https://get.acme.sh | sh
或者
git clone https://github.com/Neilpang/acme.sh.git
```
获取域名绑定商提供的 DNS API ，还有登陆邮箱  

![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2p3LXN0YXIvbXlGaWd1cmViZWQvbWFzdGVyL2ltZy8yMDE5MTIxMTE3NTExOS5wbmc?x-oss-process=image/format,png) 

#### 常见域名服务商：
1.  CloudFlare选项： 
```shell
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Email="xxxx@sss.com"
```
证书颁发： 
```shell
~/.acme.sh/acme.sh --issue --dns dns_cf -d *.example.com //直接泛域名方式，子域名也可以使用该证书
```

2. DNSPod.cn选项
```shell
export DP_Id="1234"
export DP_Key="sADDsdasdgdsf"
```
证书颁发： 
```shell
~/.acme.sh/acme.sh --issue --dns dns_dp -d *.example.com //直接泛域名方式，子域名也可以使用该证书
```
主要是变量名不一样，剩下的只列取变量和对应的厂商：

| 域名服务商  | 变量  |- -dns简写|
| ------------ | ------------ | ------------ |
| CloudFlare  |  CF_Key<br>CF_Email |dns_cf|
|  DNSPod.cn | DP_Id<br> DP_Key |dns_dp|
|  CloudXNS.com | CX_Key<br> CX_Secret |dns_cx|
|  GoDaddy.com | GD_Key<br> GD_Secret|dns_gd|
|  LuaDNS | LUA_Key<br> LUA_Email |dns_lua|
|  DNSMadeEasy | CX_Key<br> CX_Secret |dns_me|
|  Amazon Route53| AWS_ACCESS_KEY_ID<br> AWS_SECRET_ACCESS_KEY|dns_aws|
|  Linode  | LINODE_API_KEY |dns_linode|
|  FreeDNS | 账号密码<br>FREEDNS_User<br> FREEDNS_Password |dns_freedns|
|  DigitalOcean  |DO_API_KEY |dns_dgon|
|  NS1.com | NS1_Key|dns_nsone|
|  DNSPod.com | DPI_Id<br> DPI_Key|dns_dpi|

#### 最后设置证书保存位置，自动更新会同步更新保存证书的位置
```shell
#设置证书和密钥存放位置，.cer就是证书，需要更换证书格式可以找个在线更换的网站
~/.acme.sh/acme.sh  --installcert  -d  gojw.xyz   \
        --key-file   /usr/local/gojw.xyz.key \
        --fullchain-file /usr/local/fullchain.cer \
```













