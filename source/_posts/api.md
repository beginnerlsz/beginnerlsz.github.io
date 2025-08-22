---
layout:     post   				   # 使用的布局（不需要改）
title:      api 漏洞				    # 标题 
categories: SRC                  # 分类
date:       2024-08-01 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								#标签
    - src
    - api
description: api漏洞挖掘思路
---
# API漏洞挖掘

## 1. API文档

找到一个请求特定资源的api端点路径，可以逐步删除每一层路径直至发现api文档

例如：`/api/swagger/v1/users/123` 可以依次审查下面的路径

- `/api/swagger/v1/users/`
- `/api/swagger/v1/users`
- `/api/swagger/v1/`
- ...

## 2. 识别API端点

通过与API端点交互的方式来确定API端点的详细信息

1. API端点支持的 `HTTP` 请求方法

   通常可以用 `OPTIONS` 请求方法来确定所请求资源支持的请求方法

2. API端点支持的 `Content-Type` 类型

   可以从报错信息中发现所支持的 `Content-Type` 类型

   而且也可能从不同的 `Content-Type` 类型中发现漏洞

## 3. 发现隐藏参数

对同一API端点不同的请求方法包含的请求体或者返回的结果中参数可能会有差异，对比这些差异从而发现可利用的参数

例如：对 `/api/users` 

1. `PATCH` 系统发出的请求中，请求体中包含的参数为

   ```json
   {
       "username": "wiener",
       "email": "wiener@example.com",
   }
   ```

2. 但是对于 `GET /api/users/123` 请求方法，返回结果中包含的参数

   ```json
   {
       "id": 123,
       "name": "John Doe",
       "email": "john@example.com",
       "isAdmin": "false"
   }
   ```

所以可以尝试在 `PATCH` 方法的请求体中加入 `isAdmin` 





