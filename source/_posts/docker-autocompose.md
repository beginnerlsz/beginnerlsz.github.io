---
title: docker-autocompose自动生成compose配置文件
author: z1
date: 2026-04-01 11:51:53
catalog: true
tags: 
  - docker
  - docker-compose
description: 利用docker-autocompose为正在运行的docker生成compose配置文件
---

## docker-autocompose介绍

docker-autocompose是一款Docker管理工具，可以从正在运行的Docker容器中生成对应的docker-compose配置文件

## 使用方法

### 1. 拉取镜像

``` bash
docker pull ghcr.io/red5d/docker-autocompose:latest
```

### 2. 生成docker-compose文件

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose:latest <container name or ID>
```

该命令会输出对应容器完整的docker-compose配置到终端

### 3. 导出所有容器配置

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose:latest $(docker ps -aq)
```

### 4. 保存配置文件

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose:latest <container name or ID> > compose.yml
```

该命令会将对应容器的配置信息保存到compose.yml中
