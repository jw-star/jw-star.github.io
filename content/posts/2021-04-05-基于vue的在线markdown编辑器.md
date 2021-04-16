---
authors: jw
title: 基于vue的在线markdown编辑器
tags:
  - aa
  - bb
date: '2021-04-05'
lastmod: '2021-04-05 18:16:40'
---
# 基于vue2+mavon-editor的MarkDown在线编辑器

## 特点

1. 设置github参数，仓库路径就可以编辑仓库存在的文章

2. 自动生成markdown`元数据`,`时间`,`文章标题`

3. 可以创建新的markdown文件,无需填写文件名

#### mavon-editor 现在不支持vue3，所以采用Vue2


## 预览

{{< image src="https://i.loli.net/2021/04/05/8aIOgEkrFAKGLcw.png" caption="预览 (`1`)" src_s="https://i.loli.net/2021/04/05/8aIOgEkrFAKGLcw.png" src_l="https://i.loli.net/2021/04/05/8aIOgEkrFAKGLcw.png" >}}

![image.png](https://i.loli.net/2021/04/05/OospHq5yV1BJjGA.png)


## Project setup
```
npm install
```
{{< typeit code=js >}}
npm install
{{< /typeit >}}

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

{{< mapbox 116.4034138534206 39.9240913672106363 10 false "mapbox://styles/mapbox/streets-zh-v1" >}}
