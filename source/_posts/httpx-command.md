---
title: httpx_command
author: z1
date: 2025-09-26 17:17:45
catalog: true
tags: 
  - tools
  - httpx
description: 整理自己在使用httpx对不同站点进行资产存活探测时的cmd命令
---

# 网易

```cmd
httpx.exe -l url.txt -o wangyi.html -html  -td -title -status-code -mc 200 -fl 0,6,649,7367,1026
```

- 649是openresty的默认页面
- 0,6,7367,1026是蛋仔派对的活动页面，没有用
