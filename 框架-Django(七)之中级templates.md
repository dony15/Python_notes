# 框架-Django(七)之中级templates

[TOC]



### 1.概念

模版由两部分组成:**HTML**和**逻辑控制代码**

实现**内容**与**业务分离**



##### 模版处理

加载-->渲染



### 2.常用语法

##### 输出值{{}}

```
{{输出值}} #可以使用变量和对象等
{{对象.属性}} #支持点语法,不限于对象和属性,字典,方法等都可以(注意,方法使用时不能传递参数)
```



##### 逻辑控制标签{% %}

```python
#判断 if
{%if num>5%}
	{{num}}
{%endif%}

#判断 elif
{%if 表达式1%}
	代码
{%elif 表达式2%}
    代码
{%else%}
	代码
{%endif%}

#判断相等 ifequal
{%ifequal 值1 值2%} #如果值1==值2
	此处代码
{%endifequal%}

#判断不相等 ifnotequal
{%ifnotequal 值1 值2%}
	此处代码
{%endifnotequal%}

#循环 for
{%for num in nums%}
	{{num}}
    {{forloop.counter}} #循环次数
{%empty%} #列表不存在或为空时执行
    您搜索的内容不存在
{%endfor%}

#注释 comment
{% comment %}
	此处为注释的代码
{% endcomment%}

#include
{%include '模版目录' 参数1 参数2 ... 参数n%}

#url 反向解析
{%url 'namespace:name' 参数1 参数2 ... 参数n%}

#csrf_token 跨站请求伪造保护
{%csrf_token%}

#block extends 模版继承
在下面5章节
#autoescape html转义
在下面6章节
#widthratio 乘除运算
{% widthratio num 1 5 %} #num/1*5 ,num*5的效果
{% widthratio num 5 1 %} #num/5*1 ,num/5的效果
```



##### 注释语法{# #}

```
{# 这是注释 #}
```



### 3.过滤器

##### 本质

过滤器就是个函数,也可用来传递参数(参数用引号包含起来)



##### 语法

```
 {{变量|过滤器}}
```



##### 作用

变量渲染显示前,使用过滤器修饰变量(只对本位置变量生效,其他位置不生效)



##### 过滤器列表

```python
lower #变量转为小写
upper #变量专为大写
join:‘#’ #如:{{列表|join:‘#’}},效果:将列表元素用#连接显示(python中同样原理)
default:'默认值' #如果变量为空|未使用|False,则可以使用默认值生效
date:'y-m-d ' #转换日期类型的显示格式
escape #HTMl转义
add:10 #加法
add:-10 #减法
```



### 4.反向解析

##### 总urls处理

```python
url('^',include('myapp.urls',namespace='app'))
```



##### 分urls处理

```python
url(r'^city/(\d+)/$',views.city,name='city')
```



##### 模版连接

```html
<a href="{%url 'app:city' 1%}">点击查看城市</a>
```



### 5.模版继承

##### base模版(父模版)

一般使用模版继承可以去除html中重复部分,可以编写一个base基础模版作为父模版

1. 移动端:一般用来定义头尾部分
2. PC端:用来定义更复杂的部分,如导航,菜单等



##### block

在父模版中,留出指定位置

在子模版中,填充父模版中的位置

##### extend

子模版继承父模版,子模版第一行使用

注意,此时子模版不需要重复填写html头信息,全部套用父模版配置(也可以自定义)



##### 代码演示

base模版

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Base模版</title>
</head>
<body>
<center>
<div>
    导航信息
</div>
</center>

<div>
    {% block main1 %}
    {% endblock %}
</div>
<div>
    {% block main2 %}
    {% endblock %}
</div>

<center>
    <div>
    底部信息
    </div>
</center>
</body>
</html>
```



自定义模版index

```html
{% extends 'myapp/base.html' %}

{% block main1 %}
    <div>大家好，我是main1的信息哦</div>
{% endblock %}
```



自定义模版index2

```html
{% extends 'myapp/index.html' %}

{% block main2 %}
    <div>这是index2的main2信息，继承两次效果</div>
{% endblock %}
```

此时访问自定义模版,即可嵌套base模版,继承可以传递,如上,index2也可以继承index获得base的block位置



### 6.html转义

后端向前端传递信息包含h5标签时使用

```
return bender(request,’myapp/index‘,{'code':"<h1>巴拉巴拉</h1>"})
```

默认情况,后端传递的h5标签不会被解析渲染,而是转义成普通字符串显示

```
{{code}} #默认转义作字符串输出
{%code|escape%} #强制转义作字符串输出

#方法1,解决一个时可以使用过滤器关闭转义
{%code|safe%} #不转义,则html5标签渲染输出

#方法2,解决一堆时可以使用自动转义标签
{%autoescape off%} #关闭自动转义,则html5标签渲染输出
	{{code}}
	{{code}}
{%endautoescape%}

{%autoescape on%} #打开自动转义,不进行渲染,强制转义作字符串输出
	{{code}}
	{{code}}
{%endautoescape%}
```



### 7.CSRF

##### 跨站请求伪造

某些恶意网站通过表单,按钮,js等利用我们的url和用户认证信息,攻击服务器

(copy)

##### 

##### 防御措施1🌟

Settings.py中添加CSRF中间件(默认添加)

```
MIDDLEWARE = [
    'django.middleware.csrf.CsrfViewMiddleware'
]
```

缺点:

将自己的表单请求也会被拦截



##### 防御措施2🌟

表单中添加{%csrf_token%}

缺点:

该表示也会被复制,如果使用该方法,需要将此处的token信息隐藏在某个角落



##### 防御措施3🌟🌟🌟

使用验证码提交,结合防御措施2

缺点:

暂无,可以防止循环请求和非法复制请求

