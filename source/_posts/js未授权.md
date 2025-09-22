---
layout:     post   				    # 使用的布局（不需要改）
title:      未授权 				    # 标题 
categories: SRC                        # 分类
date:       2024-11-11 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								# 标签
    - src
description: 通过js挖掘漏洞的思路
---
# 未授权漏洞

## 1. 敏感接口探测

- 网站功能不全

  看到一些网站提供的功能，牢记 ***增删改查*** 四个功能。尤其注意一些网站没有在页面上提供的功能，利用同一页面上可用的功能的接口，在js中查找，有可能会发现其他隐藏接口

- 有些地方进不去，需要邀请或者授权

  利用 `findsomething` 查找接口，然后把接口补充完整(*主要是补充一些参数值*)。然后在bp里面进行测试
  
- 

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

## 3. 功能性路由与权限菜单

#### 1. 分类

1. 全开放

   返回该站点全部功能菜单。这种情况最简单，只需要修改返回数据包中某个字段的值就可以让前端页面把所有功能都显示出来

   ```http
   200 
   xxxx
   
   {
     "id":1,
     "name":"庆尘",
     "menus":[{
       {
          title: "首页",
         "url":"/index",
         "isshow":"1"
       },
       {
          title: "信控优化",
         "url":"/controlOptimization"
         "isshow":"0"
       },
       {
         "title":"信控大屏",
         "url":"/signalScreen"
         "isshow":"0"
       },
        {
         "title":"账号管理"
         "url":"/accountAccess"
         "isshow":"0"
       }
     ],
     "sex":1
     xxxx
   }
   ```

   如上方返回包，修改返回包中`isshow`的值为`1`

2. 半开放

   只返回当前用户可以使用的权限菜单。这种类型需要拿返回的权限菜单路由去js文件中查找其他未返回的权限

   ```http
   200 
   xxxx
   
   {
     "id":1,
     "name":"庆尘",
     "menus":[{
       {
          title: "首页",
         "url":"/index"
       },
       // 下面这个菜单最初是不存在的，通过js中搜索上面菜单项，进而找到下方菜单
       {
         title:"用户",
         "url":"/usermanage"
       }
     ],
     "sex":1
     xxxx
   }
   ```

   拿`/index`去js中搜，尝试找到其他权限菜单。

3. 0开放。即功能路由的返回数据包中没有任何功能菜单项，查找功能菜单项的方法如下：

   1. js搜常见关键字

      - menus/menu

      - router

   2. js搜接口名

      - getmenumodel

      - ViewModel

   3. 查找历史数据包

   4. 其他接口回显的权限菜单

   > `i`和`ii`中的关键字一般和权限菜单相关，因此遇到0开放功能性路由可以搜索上述关键字来查找权限菜单项。
   >
   > 但是这里会出现另外一个问题，无法确定每一个权限菜单项都包含哪些键值对。这就需要结合`iii`和`iv`来发现权限菜单都包含什么参数。
   >
   > 对于0开放功能路由的测试就强调了未授权测试思路：
   >
   > 遇到一个站点
   >
   > 1. 测常规漏洞
   >
   > 2. 测试当前已有功能点
   >
   > 3. 测路由
   >
   > 4. 测接口

   - 0开放实例

     ```http
     /user/getuserinfo
     
     200 
     xxxx
     
     {
       "name":qincghen
       "id":1,
       "menus":[],
       "sex":1
       xxxx
     }
     ## 上方就是0开放功能路由，返回包中一个权限菜单都没有，通过跑接口发现有个接口泄露了所有权限菜单，成功实现越权
     
     /user/allVewmodels
     
     200
     
     {menus:[{"用户管理"："/usermanage",......}]}
     ```

4. 特殊半开放

   ```http
   200 
   xxxx
   
   {
     "id":1,
     "name":"庆尘",
     "menus":[{
       {
          title: "首页",
         "url":"/index",
       }
     ],
     "sex":1
     xxxx
   }
   ```
   上方是原始数据包

   1. 在js中搜索`/index`

   2. 发现很多功能性接口，如`/usermanage`

   3. 抓响应，该包，发现页面没有变化
   4. 直接访问`/usermanage`,返回401/403/404

   ::: important
   
    这里就是半开放路由的特殊之处，此处存在路由守卫，正确的测试方式是首先抓取相应包修改，再去访问`/usermanage`,成功越权
   
   :::
   
   ```http
   xxx.com/usermange
   
   200 
   xxxx
   
   {
     "id":1,
     "name":"庆尘",
     "menus":[{
       {
          title: "首页",
         "url":"/index",
       },
       {
          title: "用户管理",
         "url":"/usermange",
       }
     ],
     "sex":1
     xxxx
   }
   ```
   
5. 特殊0开放

   ```http
   200 
   xxxx
   
   {
     "id":1,
     "name":"庆尘",
     "sex":1,
     "userstatus":0,
     xxx
   }
   ```

   直接修改`userstatus`的值 `0->1`,前端回显所有权限菜单。

## 未授权漏洞fuzz技巧

### 1. 加参数

有时候访问某个接口发现返回500，这时可以在请求包中添加两个参数，下面是一些fuzz参数

```tex
pageNo pageno
pageSize pagesize
page size
curpage pagesize
curpage rows
pageNum limit
```

这样可能就会有新发现，根据后端返回的报错提示，进一步添加参数。

### 2. 分析js找接口的情况

- 缺少操作数据的某一个功能

  **增删改查**四个功能不全

- 没有突破点的页面

  登录平台，后台功能无法使用，空白页

- 对于某些权限没有功能

  某些功能需要更高权限，可能只是前端限制，这时候就需要根据可以使用的功能和接口去查找不能用的功能对应的接口

### 3. 找接口的方法

- 根据当前页面已有/可以用的功能点的接口去js中搜索，从而找出新的接口

  ::: tips
  
  利用这种方法，还可能找出接口api网关和baseurl
  
  :::
  
- 通过浏览器找接口

  `findsomething` -> 右键选中一个接口(复制) -> 按住Ctrl鼠标点击接口左侧箭头定位到接口所在js文件 -> `Ctrl+F`搜索复制的接口

### 4. 找参数的方法

- bp插件`CaA`

- 一个页面中针对同一批数据的不同接口，有的接口可用，有的接口不可用，可以利用可以用的接口的参数去猜测+js查找从而确定不可用接口参数。

  ::: tips

  对于参数的具体写法，可以根据当前页面中同源可用接口参数的写法，同一个站，接口规范是相同

  :::

- 利用返回包的报错信息，结合可用接口的参数格式



