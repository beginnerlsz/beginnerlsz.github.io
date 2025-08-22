---
layout:     post   				    # 使用的布局（不需要改）
title:      ssrf                # 标题
categories: SRC                        # 分类
date:       2024-11-17 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								#标签
    - src
    - SSRF
description: SSRF漏洞挖掘思路
---
# SSRF 漏洞挖掘

## SSRF类型

### 1. 全回显SSRF

### 2. 无回显SSRF(shellshock 命令执行)

- `BP`插件--`collaborate everywhere` 探测注入点

  BP: `target`->选中对应站点，右击`Add to Scope`->`yes`->目标网页上浏览点击，以便插件寻找定位漏洞

- `shellshock+header` 搜索`payload`

无回显`SSRF RCE`从而达到危害最大化

## SSRF绕过

### 1. 绕过某种特定限制

1. 基础版

   `@` : 绕过域名

   当后端对域名做了限制，导致域名不可更改

   `http://www.baidu.com@10.1.1.1` 实际上访问的时10.1.1.1

2. 升级

   `\` + `@` 绕过

   `http://www.xxx.com/proxy?url=http:///10.1.1.1` -> 403

   `http://www.xxx.com/proxy?url=http:///10.1.1.1\@www.xxx.com`  -> 200

   成功访问到内网

3. 绕过后缀

   `/#/`

   用于绕过后缀不可更改

   `http://10.1.1.1:80/#/a.jpg` -> `http://10.1.1.1:80`

### 2. DNS重绑定

利用爆破或者并发绕过

### 3. 302重定向

利用vps作为跳板，访问vps，进而跳转到内网中





