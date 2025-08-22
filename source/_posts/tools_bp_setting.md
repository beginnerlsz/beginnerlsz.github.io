---
layout:     post   				    # 使用的布局（不需要改）
title:      burpsuite配置				    # 标题
categories: bp                        # 分类 
date:       2024-11-11 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								# 标签
    - src
    - burpsuite
description: 挖洞BP设置
---
# 挖洞环境配置

## bp批量自动改包

- **应用场景：存在权限校验的场景可以尝试**

  因为在有些站点只在前端进行了校验，后端未作验证，可以用这种方法绕过，从而获取高级权限。

- 操作方法：Proxy -> Proxy settings -> Tools -> Proxy -> Match and replace rules  改响应包，即 `response body`

  ![image-20241030205436086](./images/image-20241030205436086.png)

## 过滤杂包

- 浏览器代理插件设置，走bp代理的模式下将不走代理的域名写入不代理的地址列表那一栏。

  ![Snipaste_2024-10-30_21-10-33](./images/Snipaste_2024-10-30_21-10-33.png)

- bp中设置，bp中对应的包中右键然后选中 `Don't intercept requests -> To this host`,这样就会跳过该包对应的host。这个方法和下面的方法相同的原理。

- 在bp的 `Proxy settings` 中设置，如下图位置

  ![Snipaste_2024-10-30_21-15-39](./images/Snipaste_2024-10-30_21-15-39.png)

