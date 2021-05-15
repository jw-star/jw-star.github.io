---
category: linux
layout: post
tags:
  - centos
  - actions
  - github
title: githubActions部署文件到服务器
date: 2021-04-03
---



## ssh秘钥方式
首先在服务器生成秘钥
参考 ：[参考](https://github.com/easingthemes/ssh-deploy)
```bash
      - name: Deploy to Staging server
        uses: easingthemes/ssh-deploy@v2.1.6
        env:
          SSH_PRIVATE_KEY: ${{ secrets.DEPLOY_KEY }}
          ARGS: "-rltgoDzvO"
          SOURCE: './public/' #仓库根路径
          REMOTE_HOST: ${{ secrets.SSH_HOST }}
          REMOTE_USER: ${{ secrets.SSH_USERNAME }}
          TARGET: '/www/wwwroot/blog.gojw.xyz/mybolg/'  #目标服务器的路径
          EXCLUDE: "/dist/, /node_modules/"   #排除文件夹
```
## 密码方式

```bash
      - name: SSH Server Deploy   
        uses: kostya-ten/ssh-server-deploy@v4
        with:
          host: ${{ secrets.SSH_HOST }}
          port: ${{ secrets.DEPLOY_PORT }}
          username: ${{ secrets.SSH_USERNAME }}
          password: ${{ secrets.DEPLOY_PASSWORD }}
          scp_source: ./public/
          scp_target: /www/wwwroot/blog.gojw.xyz/mybolg/
```

测试两种action ， ssh的方式速度快，账号密码的 至少3分钟，ssh几秒就可以完成。
