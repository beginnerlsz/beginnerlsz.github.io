---
layout:     post   				    # 使用的布局（不需要改）
title:      app&小程序抓包 				    # 标题 
categories: SRC                        # 分类
date:       2024-11-11 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								# 标签
    - src
    - burpsuite
description: 小程序/app漏洞挖掘方法
---
# 小程序&app抓包

## 小程序

### 1. Proxifier

1. Profile -> Proxy Servers -> add 新建一条代理，指向bp

2. Profile -> Proxification Rules -> add 新建一条代理规则

   ![proxifier代理规则](./images/Proxifier_proxy_rules.png)

### 2. Reqable

1. bp新建一条监听规则

   ![bp新建监听规则](./images/bp_addListen.png)

2. Reqable联动bp

   代理 -> 二级代理 -> 新建

   监听地址和端口填写bp中新建的监听器的

   ![Reqable-settings](./images/Reqable-settings.png)



