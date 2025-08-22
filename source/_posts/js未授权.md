---
layout:     post   				    # 使用的布局（不需要改）
title:      js未授权 				    # 标题 
categories: SRC                        # 分类
date:       2024-11-11 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								# 标签
    - src
description: 通过js挖掘漏洞的思路
---
# JS未授权漏洞

## 1. 敏感接口探测

- 网站功能不全

  看到一些网站提供的功能，牢记 ***增删改查*** 四个功能。尤其注意一些网站没有在页面上提供的功能，利用同一页面上可用的功能的接口，在js中查找，有可能会发现其他隐藏接口

- 有些地方进不去，需要邀请或者授权

  利用 `find something` 查找接口，然后把接口补充完整(*主要是补充一些参数值*)。然后在bp里面进行测试

## 2. 手动查找JS中的隐藏接口

是findsomething，经过测试并无可以利用的点，而此时也没有一个要寻找的目标关键字，那我们就只能通过查找Js中的接口关键字来寻找隐藏的接口

寻找接口关键字

```markdown
type:"POST"
type: "GET"
post("
get("
ashx?
ashx
url:
url:"
url:'
path:
path:"
path:
'action?
data
params
```

## 未授权漏洞技巧

有时候访问某个接口发现返回500，这时可以在请求包中添加两个参数

```json
{
    "PageNo": 1,
    "PageSize": 10
}
```

这样可能就会有新发现


