# 框架-Django(六)之中级view

[TOC]



### 1.概念

settings中的`ROOT_URLCONF = '项目名.urls'`根级URL,Django默认配置好的

`urlpatterns`:url实例,存储访问路径的列表(2.0后支持path方式),

**注意:url设计遵循原子性和最小范围原则**



##### url方式存储列表

```
#默认使用正则
参数可以用()的形式标注,如‘^students/(\d+)$’ 表示接受数字类型参数
url头不需要加反斜杠/ 
url前如果用r标注,则表示url中,字符串不会进行正则转义(特殊符号还是会转义)
```

##### path方式存储列表

```

```



##### rul反向解析

在视图/模版中使用了硬编码地址,地址发生变化时使用,动态生成url路径

在使用url时,通过url配置,动态生成url路径

场景:模版



### 2.流程

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fykh5svpsdj30u00u0wl8.jpg)



### 3.视图参数HttpRequest

1. `Httprequest`实例
2. 其他参数(url正则中定义的参数)

------

##### Httprequest

Django接收到Http请求后,会根据报文创建一个HttpRequest对象,该对象默认为视图的第一个参数

Httprequest对象列表

```python
#request 属性
path  #请求的uri(不包含ip和端口)
method #请求方式 如 GET POST等
encoding #请求的编码方式,一般为utf-8
QueryDict GET #类似字典的对象,包含了get请求的所有参数
QueryDict POST #类似字典的对象,包含了post请求的所有参数
FILES #类似字典的对象,包含了所有上传文件
COOKIE #类似字典的对象,包含了所有Cookie对象
session #类似字典的对象,包含了当前session会话

#request 方法
is_ajax() #判断是否ajax请求,XMLRequest,如果是,返回True

#QueryDict对象
#request 中,GET/POST请求的返回值
get(key) #获取key对应的一个值,如http://localhost:8000?a=1&b=2,可以通过a获取1
getlist(key) #列表的形式获取key对应的值,如http://localhost:8000?a=1&a=5&a=6&b=2,可以通过a获取[1,5,6]
```



### 4.视图响应HttpResponse

##### 4-1.httpresponse 响应JSON

```python
#属性列表
content 响应内容
content-Type 响应MIME类型,如 text/html
status_code 响应状态码,如200 302等
charset 响应编码
```



```python
#方法列表
init() 使用页面内容,实例化HttpResponse对象
write(content) 文件的形式响应内容
flush() 文件的形式刷新缓冲区
set_cookie(key,value="",max_age=None,exprise=None) 设置cookie
delete_cookie(key) 删除cookie,cookie不存在时不生效
```



##### 4-2.render

响应页面(模版)+数据,返回完整的html

render(request,template[,content])

`request`请求远行

`template` 模版页面

`content` 渲染内容

------

##### 4-3.HttpResponseRedict

重定向

简写`redirect(重定向页面)`



------

##### 4-4.JsonResponse

返回JSON,一般异步ajax使用

数据为dict字典形式存储

Content-Type默认application/json类型



### 5.状态保存

##### 5-1.存储位置

1. cookie
2. session



##### 5-2.启用session

settings.py中默认启用

```python
INSTALLED_APPS = [
    'django.contrib.sessions'
]

MIDDLEWARE = [ #中间件
    'django.contrib.sessions.middleware.SessionMiddleware'
]
```



##### 5-3.使用session

启用session后,每个HttpRequest对象都有session属性,一个类似字典对象

```python
session['Dony15']="session_value" #设置session
get(key,default=None) #根据key获取session值,如果获取不到,则使用默认值
clear() #清空session
flush() #删除当前会话,并删除对应cookie
```



**logout()** 

删除当前会话登出,并删除对应cookie ,推荐使用,该方法是单独封装logout模块,并非session方法



##### 5-4.设置session

```python
set_expiry(value) #设置过期时间(单位秒),默认两个星期 ,0关闭浏览器失效,None永不过期
```



##### 5-5.存储session

默认数据库`Django_session表中`



`setting.py`中配置session引擎(SESSION_ENGINE)

```python
SESSION_ENGINE="django.contrib.session.backends.db" #默认启动,存在数据库中

SESSION_ENGINE="django.contrib.session.backends.cache" #存储到本地缓存中

SESSION_ENGINE="django.contrib.session.backends.cached_db" #存储到本地缓存和数据库中,优先本地查询
```



##### 5-6.redis缓存session,分布式可用

```python
SESSION_ENGINE = 'redis_sessions.session'
SESSION_REDIS_HOST = 'localhost'
SESSION_REDIS_PORT = 6379
SESSION_REDIS_DB = 0
SESSION_REDIS_PASSWORD = '123456'
SESSION_REDIS_PREFIX = 'session'
```





### 6.错误页面

##### `setting.py`配置



```
DEBUG=False #如果为True,则永远不会出现404等错误页面,默认True
ALLOWED_HOSTS = [’*‘] #允许访问的ip,此处*表示所有人都可以访问
```



##### `404`

页面写在templates中,404.html

`request_path`:自动匹配用户访问的url,不需要函数再次实现

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>404</title>
</head>
<body>
<h1>您查找的页面不见了</h1>
<h2>{{ request_path }}</h2>
</body>
</html>
```

