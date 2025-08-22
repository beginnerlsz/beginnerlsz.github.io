---
layout:     post   				    # 使用的布局（不需要改）
title:      hunter查询语法 				      # 标题
categories: 信息收集                        # 分类 
date:       2024-11-17 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								#标签
    - src
    - hunter语法
description: hunter 查询语法
---
# hunter 查询语法

- 京东

  ```tex
  (domain="jd.com" or domain="jdcloud.com" or domain="wuliujie.com" or domain="jdl.com" or domain="jdx.com" or domain="selling.cn") and domain!="qianxun.com" and web.title!="Welcome to" and web.title!="京东(JD.COM)-正品低价" and web.title!="Investor Home" and web.title!="您所访问的页面不存在" and web.title!="undefined" and web.title!=="京东" and web.title!="出错了" and web.title!=="" and icp.name="京东" and header.status_code=="200" and title!="故障" and title!="不存在" and title!="堡垒" and title!="京东云"
  ```

- 百度

  ```tex
  domain="baidu.com" and icp.name="百度" and header.status_code=="200" and web.title!="百度新闻" and web.title!="百度一下" and web.title!="百度地图" and web.title!="页面不存在" and web.title!="" and web.title!="百度网盘" and web.title!="welcome" and web.title!="/(ㄒoㄒ)/~~" and web.title!="出错"

  ```
