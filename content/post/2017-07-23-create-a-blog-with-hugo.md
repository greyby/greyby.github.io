---
title: "用 Hugo 搭建个人 Blog"
date: 2017-07-24T01:05:44+08:00
slug: "create a blog with hugo"
description: "使用 Hugo 搭建个人 Blog, 通过 Travis 自动把文章发布到 Gihub Pages"
---

## 为什么选择 Hugo

现在各种 Blog 系统实在太多了， 我在面对众多选择时主要考虑了三点:

1. 支持 `Markdown` 格式

	把 `Markdown` 写的笔记心得之类的发出去， 是我个人 Blog 存在的主要目的。而且用所见即所得编辑器排版，效率不如写文章的时候就用 `Markdown` 排完版来的快

2. 尽量不需要外部依赖

	不想单独用 VPS 或者云主机，能直接放到[Github Pages](https://pages.github.com/) 或者 [Coding Pages](https://pages.coding.net/) 最好

3. 生成速度要快	

	工具的效率决定人的效率，不能因为这个浪费时间。

有前两点要求，直接排除了 [Medium](https://medium.com/) 和 [Ghost](https://ghost.org/)，只剩下静态网站生成工具了。 `Medium` 的编辑器做的很好用，但是不直接支持 `Markdown`。虽然可以通过第三方工具将 `Markdown` 写的文章导入，但感觉太麻烦，而且对表格的支持也不好。 `Ghost` 默认主题很漂亮，也支持 `Markdown`，但是依赖 `MySQL` 数据库，也只好放弃。

静态网站生成工具是在本地安装程序，写文章后自动生成静态页面，静态页面可以直接放到 `web server` 上。所以用这类工具服务器上不需要什么依赖。静态网站生成工具被 `Github Pages`带火后，基本每种编程语言都有自己的实现。 `Ruby` 开发的 [jekyll](http://jekyllrb.com/)、[Octopress](http://octopress.org/)、[Middleman](https://middlemanapp.com/)，`Node.js` 的 [hexo](https://hexo.io/)，`Go` 的[hugo](http://gohugo.io/)。`jekyll` 和 `hexo`都被人诟病文章多了后生成速度太慢。虽然我有可能心血来潮写一篇就停止了，但是程序员总是要追求效率，所以最后选了 `hugo`。

## 安装

macOS 下面，用 `brew` 安装大部分软件都是非常方便的。 `brew install hugo` 之后等着就好了。

## 使用

### 创建 Blog

`hugo new site blogs -f yaml`

`hugo` 默认使用 `TOML` 格式的配置文件，我个人还是更喜欢 `YAML`， 通过 `-f yaml` 可以指定使用 `YAML` 格式的配置文件。生成网站的默认目录如下

```
tree -L 1
.
├── archetypes
├── config.yaml
├── content
├── data
├── layouts
├── static
└── themes
```

其中比较重要的目录 

* `content` 放文章源文件
* `static` 生成的静态文件 
* `themes` 主题文件

使用 `git` 管理网站源码 

```shell
cd blogs
git init
git add .
git commit -m 'initial commit'
git checkout -b source
# 下面会说到 master 另有它用
git branch -d master
```

### 安装主题 

挺喜欢 `Ghost` 默认主题 `Casper`， 有人开发了 `Hugo` 的实现 [https://github.com/vjeantet/hugo-theme-casper](https://github.com/vjeantet/hugo-theme-casper)。 把主题当作 `submodule` 管理，可以随时与主版本同步。需要根据主题提供的参考代码调整配置 `config.yaml`。

```shell
git submodule add https://github.com/greyby/hugo-theme-casper.git themes/casper
git submodule update --remote
```

### 发布文章

`hugo new post/2017-07-11-productive-macos-hammerspoon.md`

### 运行

`hugo server -t casper`  

访问 http://localhost:1313/ 可以预览

### 生成静态页面

运行 `hugo -t casper`，生成的静态页面在 `public` 目录下

### 部署到 Github Pages

Github Pages 有  [`User/Organization Pages`](https://help.github.com/en/articles/user-organization-and-project-pages#user-and-organization-pages-sites) 和 [`Project Pages`](https://help.github.com/en/articles/user-organization-and-project-pages#project-pages-sites) 两种类型。`User/Organization Pages` 可以通过 `https://<username>.github.io` 直接访问。网站的静态文件放在 `<username>.github.io` 仓库的 `master`分支。为了方便，直接把 `public` 目录当作 `<username>.github.io` 的 `master` 分支，把 hugo 生成的网站代码放到 `source` 分支。这样就能用  `<username>.github.io`  一个仓库对 blog 的源码及生成的静态文件进行管理。

- master

  生成的静态文件

- source

  网站的源码

```shell
git remote add origin https://github.com/greyby/greyby.github.io.git
git push -u origin source

git submodule add https://github.com/greyby/greyby.github.io.git public
cd public
git push -u origin master
```



### travis 自动发布

每次发布新的文章，都要手动生成静态页面，然后将静态页面传到 `Github` 未免太过麻烦，借助持续集成工具 `travis`，可以做到有新文章提交后自动生成静态页面然后推送到 `master` 分支。`travis` 通过数字签名的方式来实现 `Github` 的免密码登录和 `push` 权限获取。

先登录 https://travis-ci.org/  并激活项目 `<username>.github.io`。



* 安装 `travis` 并登录

  `gem install travis`

  `travis login --github-token`

  `Github` 开启两步认证的要通过 token 登录，需要的权限参考 [^github_authority]

  [^github_authority]: [Github 用户权限](https://docs.travis-ci.com/user/github-oauth-scopes/)

* 生成 RSA 密钥

  `ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ~/.ssh/travis_key`

​	会生成公私钥对  `travis_key.pub` 和  `travis_key` 


* 生成加密文件`travis_key.enc`

  为了防止私钥的泄露，使用 `travis` 提供的工具将私钥加密，在 repo 根目录执行

  ```shell
  touch .travis.yml
  travis encrypt-file ~/.ssh/travis_key --add
  ```

  私钥加密后生成 `travis_key.enc`文件，并将解密的命令自动写入 `.travis.yml` 中。再调整配置满足自动发布文章，内容参考[.travis.yml](https://github.com/greyby/greyby.github.io/blob/source/.travis.yml)

* 在 Github 上给项目添加 `deploy key`

  为了让 `travis` 有 push 权限，在项目的 `Settings -> Deploy keys` 新增，将公钥 `travis_key.pub` 的内容拷贝进去

* 发布脚本

  参考 [deploy.sh](https://github.com/greyby/greyby.github.io/blob/source/scripts/deploy.sh)

这样 `source` 分支有 push ，就会触发 `travis`自动部署网站。
