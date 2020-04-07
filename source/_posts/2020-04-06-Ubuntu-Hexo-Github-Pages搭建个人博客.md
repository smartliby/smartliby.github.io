---
title: Ubuntu+Hexo+Github Pages搭建个人博客
date: 2020-04-06 14:24:21
categories:
- hexo
tags:
- hexo
- ubuntu
- gitHub pages
- zeit.co
---

我的个人博客其实若干年前就搭建完成并投入使用了，但是比较懒一直没有写笔记，最近发现百度爬虫没有收录我的个人博客文章，研究了一下发现是github把百度爬虫给禁掉了，解决一下顺便补一下笔记吧

<!-- more -->

## 安装Nodejs

Node.js是基于Chrome V8引擎的JavaScript运行环境，npm是随Node.js一起安装的包管理工具，新版的Node.js集成了npm。

```
sudo apt install nodejs
sudo apt install npm
```

检查node版本：`node -v`

```
v8.10.0
```

检查npm版本：`npm -v`

```
6.9.0
```

## 安装Hexo

Hexo是一个基于node.js的快速、简洁且高效的博客框架，支持Markdown解析文章。简言之，hexo将Markdown编写的文章生成为静态html页面，然后部署到github。
安装hexo：`sudo npm install -g hexo`。
在指定文件夹新建所需的文件：

```
hexo init <blog-name>
cd <blog-name>
npm install
```

该位置就是hexo的工作空间，该工作空间的目录如下：

```shell
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

其中，`_config.yml`是博客网站的配置文件,`source`文件夹是存放用户资源的地方，`_posts` 文件夹存放Markdown文件（.md），`scaffolds`是模板文件夹，当用hexo新建文章时，hexo会根据`scaffolds`文件夹下的文件来建立文件，`themes`文件夹是存放主题的文件夹，hexo根据主题生成静态页面。
关于_config.yml的补充说明：其中工作空间根目录下有一个_config.yml，是博客网站的配置文件，themes文件夹中对应不同主题的目录下也有一个_config.yml文件夹，是主题的配置文件。

## 配置Hexo

### hexo常用命令

| 命令                    |                        作用                        |  简写  |
| :---------------------- | :------------------------------------------------: | :----: |
| hexo init "folder-name" |                    新建一个网站                    |        |
| hexo new "title-name"   |                    新建一篇文章                    |        |
| hexo clean              |                    清除缓存文件                    |        |
| hexo generate           |                    生成静态文件                    | hexo g |
| hexo server             | 启动服务器，默认服务器网址：http://localhost:4000/ | hexo s |
| hexo deploy             |                      部署网站                      | hexo d |

### 更换主题

默认主题为landscape，更换为next或其它主题。
下载主题命令：

```
git clone https://github.com/theme-next/hexo-theme-next
git pull
```

我一般会在自己的github保存hexo-theme-next主题，这样方便个性化修改保存，我的主题地址大家可以参考下

```
git@github.com:smartliby/hexo-theme-next.git
```

采用`git`命令克隆主题文件，以后更新可以通过`git pull`命令来快速更新，而不用再次下载安装包进行替换。
打开_config.yml文件，修改`theme`为`next`。然后运行：

```
hexo clean
hexo g
hexo s
```

打开`http://localhost:4000/`可以发现主题更改了。

## 安装配置github

首先查看是否已经安装git，`git --version`，一般ubuntu自带git。若没有安装采用`sudo apt install git`进行安装。

### 配置github

命令行输入：

```
git config --global user.name "your user name"
git config --global user.email "your email address"
```

### 创建公钥

命令行输入：

```
ssh-keygen -C 'your email address' -t rsa
```

一直按回车直至结束，结束后会在`～/.ssh/`下建立密钥文件，即`~/.ssh/id_rsa_pub`，打开该文件，复制全部内容。

### github添加公钥

点击个人头像—>settings—>SSH and GPG keys—>New SSH key，粘贴刚才复制的文本。

### 创建项目仓库

在github页面，选择New repository，Respository name输入`username.github.io`，点击确定。

## hexo将静态页面部署到github

打开hexo目录下的`_config.yml`，在最后添加：

```
deploy:
  type: git
  repository: git@github.com:username/username.github.io.git
  branch: master
```

master分支托管public目录下的静态网页代码，source分支托管markdown源码，这样就不用担心源码丢失了，就算电脑丢了也不怕，同时也方便管理，**平时在source分支开发即可**。

我平时写文章的流程：

1. hexo new "title-name"
2. `hexo g` 生成静态网页代码
3. `hexo d` push到远程master分支
4. 在source分支执行 `git add -A` `git commit -m "提交内容"` `git push`

然后安装hexo插件：

```
npm install hexo-deployer-git --save
```

最终进行部署，命令行输入：

```
hexo clean
hexo g
hexo d
```

现在就可以通过`username.github.io`访问你的博客了！

## 丰富博客功能安装一些插件或者配置

* [gitalk](https://gitalk.github.io/) 评论功能
* [busuanzi](https://busuanzi.ibruce.info/) 极简网页计数器
* local_search 搜索功能
* google_analytics 网站运行情况分析
* baidu_analytics 网站运行情况分析
* [google_site_verification](https://search.google.com/search-console) 验证网站所有权,SEO优化
* [baidu_site_verification](https://ziyuan.baidu.com/linksubmit/index) 验证网站所有权,SEO优化
* needmoreshare2 分享功能

以上是本人使用的一些插件和配置，大家参考一下，可根据自己情况选择安装和配置,具体安装和配置方法可自行google

## 绑定域名

首先申请域名，我是在[阿里云](https://homenew.console.aliyun.com/)申请的，需要备案

域名解析配置,添加一条CNAME记录

![](/images/media/选区_068.png)

github pages 上 `CNAME` 文件配置

方法1：静态源码中添加CNAME文件，内容为申请的域名并执行`hexo g` `hexo d`部署到github上

![](/images/media/选区_071.png)

方法2：直接在github.io仓库的Settings的`GitHub Pages`项直接设置`Custom domain`,github会自动添加CNAME文件

![](/images/media/选区_070.png)

现在使用申请的域名访问一下试试吧

## 解决百度爬虫无法爬取 Github Pages 个人博客问题

网上有很多方法可以使百度爬虫爬取博客页面，总结起来主要有：

1. CDN，利用云服务提供商将 blog 多做几个镜像。
2. 换托管平台，比如说国内的代码托管平台。
3. 自行使用 VPS 托管。

CDN 比较贵还不完美，不适合小流量网站，又不想换托管平台，VPS 大陆访问速度不快。

有没有简单、可靠还免费的方法？

有，真的有，[zeit.co](https://zeit.co/)就是目前比较好的一个选择，一个静态网页和无状态函数托管的云平台。试用了之后，感觉非常强大，它能够以 Github App 的形式与 GitHub 集成，接管 repo 的 CI，进行深度定制，并且能够快速部署、自动伸缩。

关键是，现在基本功能是免费的，这对于我们托管博客来说，已经很够用了。更惊喜的是，它还提供智能 CDN 和智能 DNS

![](/images/media/选区_066.png)



本质上他们提供了 Storage + CDN + DNS 这一套完整服务。根据他们的文档，整个过程我只需要：

- 用我的 Github 账户登陆
- Import Project—>Import Git Repository—>给zeit 我的静态网页 repo 的 read 权限
- 导入过程中，选择 other 模板即可，默认 ci 命令不用改，Root Directory 也不用改，导入完成后就会自动帮你 deploy。
- 等几秒，importing
- 部署成功！

不用写任何 code，点两下鼠标就完事了。部署完成后，会生成一个类似于 xxxxxxx.now.sh 的链接，点击即可访问，静态资源已经部署到了他们的边缘 CDN 节点上。

![](/images/media/选区_067.png)

接着在[zeit.co](https://zeit.co/)设置域名

Selecting Your Project—>Settings—>Domains—>Entering Your Chosen Domain—>Add the Domain

![](/images/media/选区_074.png)

这时候它会报错，因为你的域名还没有添加解析。这里，他们推荐的方式是让你的域名使用他们的Nameserver，或者说让他们提供DNS解析服务。但是我这里选择CNAME的方式。因为我一直使用的是cloudflare的DNS解析，我申请的域名之前是为梯子用的，cloudflare的CDN免费并且支持websocket，所以我这里就继续这么用了，在cloudflare上添加 CNAME 记录就行了，内容为上图的value值：`alias.zeit.co`。

![](/images/media/选区_072.png)

如果还是报错的话，你可以试着删除（先点击“Edit”按钮，然后点击“Remove”）后重新添加，如下图即表示设置成功了

![](/images/media/选区_069.png)

## 百度爬取

最后在[百度站长工具](https://ziyuan.baidu.com/)页面，在 *数据监控* > *抓取诊断*，进行测试，测试通过后选择链接提交以sitemap方式提交链接即可，之后百度就会开始爬取我们的博客了

## 参考

[1] 解决百度爬虫无法爬取 Github Pages 个人博客的问题：https://zpjiang.me/2020/01/15/let-baidu-index-github-page/
[2] zeit.co 帮助文档：https://zeit.co/docs
[3] 使用 zeit.co 托管 Hexo 静态博客：https://www.qtmuniao.com/2020/03/15/hexo-to-zeit-co/
[4] 域名绑定：https://segmentfault.com/a/1190000011203711
[5] zeit域名解析：https://blog.solariar.tech/2020/how-this-site-work-v2/