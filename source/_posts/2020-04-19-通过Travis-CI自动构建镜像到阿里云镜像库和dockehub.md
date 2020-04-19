---
title: 通过Travis CI自动构建镜像到阿里云镜像库和dockehub
date: 2020-04-19 14:57:23
categories:
- k8s
tags:
- k8s
- github
- docker
---

其实之前我已经写过一篇关于通过阿里云镜像库下载gcr.io镜像的文章，但是该方法有一个问题，每次需要新的镜像都需要在阿里云镜像库手动创建，太麻烦了，这篇文章通过**Travis CI**可以自动构建镜像到阿里云，方便好用

<!-- more -->

#### 创建github仓库

首先登录github创建一个仓库，名称自定义，仓库包含如下2个文件：

img-list.txt 外网镜像列表文件
.travis.yaml travisCI自动构建文件
示例仓库：https://github.com/smartliby/pull-docker-images

.travis.yaml 内容如下，主要从国外镜像仓库pull镜像，打tag，并push到阿里云或dockerhub

```
language: bash

services:
  - docker

env:
  global:
    # change the registry name and username/password to yourself's.
    - DOCKER_HUB=smartliby
    - ALI_REGISTRY=registry.cn-hangzhou.aliyuncs.com/smartliby

before_script:
  - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
  - echo "$ALI_PASSWORD" | docker login "$ALI_REGISTRY" -u "$ALI_USERNAME" --password-stdin
 
script:
  - echo "start pull and retag and push"
  - |
    for image in $(cat img-list.txt)
    do
      image_name=${image##*/}
      image_name=${image_name%@*}
      docker pull $image
      docker tag $image $DOCKER_HUB/$image_name
      echo $ALI_REGISTRY
      docker tag $image $ALI_REGISTRY/$image_name
    	
      # push到dockerhub
      docker push $DOCKER_HUB/$image_name
      # push到阿里云仓库
      docker push $ALI_REGISTRY/$image_name
    done

```

.travis.yaml文件监测github仓库的代码变动，当有代码变动时比如img-list.txt写入新的镜像列表时将触发tarvis的自动构建
这里需要登录阿里云或dockerhub才能执行docker push操作，其中$ALI_USERNAME这类变量在TravisCI管理界面定义好即可。

#### Travis CI配置

访问travis官网： https://www.travis-ci.com， 使用github账号登录。

开启需要进行自动化构建的仓库并设置$ALI_USERNAME这类变量即可

![](/images/media/选区_085.png)

当对github仓库执行git commit、git push操作时将自动触发构建，执行仓库中的脚本

![](/images/media/选区_086.png)

登录阿里云或dockerhub查看，img-list.txt 列表中的镜像已经成功被push上来

![](/images/media/选区_087.png)

![](/images/media/选区_088.png)

#### 参考

[1] https://blog.csdn.net/networken/article/details/84571373