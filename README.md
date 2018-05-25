# CDN

使用 Github，jsDelivr， TravisCI 搭建一个轻量靠谱的 CDN。

## 实现原理

Github 做 CDN 存储，jsDelivr 做 CDN 服务器, TravisCI 做自动更新。

## 流程

1. 本地添加文件到 Git，推送到 Github，触发 TravisCI 执行构建；
1. TravisCI 拉取最新 Github 文件，打 Tag，发布到 Github Release, 将新版本文件推送回 Github；
1. 用户访问 jsDelivr 的 CDN 服务器，jsDelivr 到 Github Release 拉取对应版本或者最新版本文件，返回给用户；
1. 本地更新文件，如此往复触发第一步。

## 核心代码

```yaml
language: node_js # 升级版本需要依赖 npm
node_js: stable

install: true # 无需安装依赖，调过安装

branches:
  only:
  - master # 只发布 master 分支

before_script:
- git config --global user.name "travis" # 配置 travis git 信息
- git config --global user.email "travis@miantiao.me"

script:
- git push -f https://$GITHUB_KEY@github.com/$TRAVIS_REPO_SLUG.git `npm version patch -m "%s [ci skip]"` # 打 Tag，发布到 Github Release, 使用 [ci skip] 调过 CI， 防止死循环
- git push -f https://$GITHUB_KEY@github.com/$TRAVIS_REPO_SLUG.git HEAD:master #将新版本文件推送回 Github
```

## CDN 效果

jsDelivr 服务器分布，有服务器位于中国。

![jsDelivr](https://cdn.jsdelivr.net/gh/ccbikai/cdn/example/jsdelivr.jpg)

## 文件浏览和流量统计

jsDelivr 提供了一个可以看 CDN 文件和使用流量的地址：<https://www.jsdelivr.com/package/gh/ccbikai/cdn> 。

[![jsDelivr File](https://cdn.jsdelivr.net/gh/ccbikai/cdn/example/jsdelivr-file.png)](https://cdn.jsdelivr.net/gh/ccbikai/cdn/example/jsdelivr-file.png)

## 演示
1. https://cdn.jsdelivr.net/gh/ccbikai/cdn/README.md
1. https://cdn.jsdelivr.net/gh/ccbikai/cdn@latest/README.md
1. https://cdn.jsdelivr.net/gh/ccbikai/cdn@0.0.1/README.md
1. https://cdn.jsdelivr.net/gh/ccbikai/cdn@0.0.5/example/1.jpg

