---
layout:     post   				    # 使用的布局（不需要改）
title:      SRC-burpsuite心跳包 				    # 标题
categories: bp                       # 分类 
date:       2025-07-28 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								# 标签
    - src
    - burpsuite
description: Burpsuite心跳包过滤
---
# Burpsuite心跳包过滤

## filter 筛选器

```java
//以下排除内容都为半匹配，即包含以下任意内容都会被过滤掉

//需要排除的域名
String[] domainExclude = {
    "freebuf.com",
    "h5.youzan.com",
    //百度
    "baidu.com",
    "baidu.hk",
    "bdstatic.com",
    "bcebos.com",
    "aiqicha.com",
    "aiqicha.cn",
    //腾讯
    "tencent.com",
    "qq.com",
    //google
    "google.com",
    "googleapis.com",
    "google.com.hk",
    "gstatic.com",
    //阿里
    "alicdn.com",
    "tool.lu",
    "github.com",
    //360
    "360.net",
    "360.cn",
    //火狐
    "mozilla.net",
    "firefox.com",
    //bing
    "bing.cn",
    "zsxq.com",
    //奇安信
    "qianxin.com"
    };

//需要排除的请求路径或文件后缀
String[] pathExclude = {"WebSocket", ".css", ".png", ".svg"};

//需要排除的HTTP方法
String[] methodExclude = {"OPTIONS", "HEAD"};

//需要排除的响应类型，包括图片、字体文件、二进制、CSS、脚本文件
//MimeType[] mimetypeExclude = {
//    MimeType.APPLICATION_UNKNOWN, MimeType.UNRECOGNIZED,
//    MimeType.FONT_WOFF2, MimeType.FONT_WOFF,
//    MimeType.VIDEO, MimeType.SOUND,
//    MimeType.IMAGE_TIFF, MimeType.IMAGE_BMP,  MimeType.IMAGE_PNG, MimeType.IMAGE_GIF, MimeType.IMAGE_JPEG, MimeType.IMAGE_UNKNOWN,
//    MimeType.CSS
//    };

//需要排除的请求体内容
String[] bodyExclude = {""};
String host = requestResponse.request().httpService().host();
String path = requestResponse.request().path();
String method = requestResponse.request().method();
String body = requestResponse.request().body().toString();
var mimeType = requestResponse.mimeType();
return Arrays.stream(domainExclude).noneMatch(it -> host.contains(it))
     && Arrays.stream(pathExclude).noneMatch(it -> path.contains(it))
     && Arrays.stream(methodExclude).noneMatch(it -> method.contains(it))
//   && Arrays.stream(mimetypeExclude).noneMatch(it -> mimeType == it)
     && Arrays.stream(bodyExclude).filter(it -> it != null && it.length() != 0).noneMatch(it -> body.contains(it));
       

```





