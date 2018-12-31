# 框架-Django(八)之高级集成

[TOC]



### 1.静态资源

##### 开发环境

设置settings.py

```
DEBUG=True #为False时会阻止静态资源直接访问
STATIC_URL = '/static/' #静态资源访问路径
STATICFILES_DIRS=[	#静态资源物理路径
    os.path.join(BASE_DIR,'static')
]
```

static目录

根项目目录中创建static包,用来存储静态资源

一般static中会根据应用来区分,即:static二级目录创建应用包

应用包内创建js/css/font/img等资源目录



html引入

```html
<script type="application/javascript" src="/static/mapp/js/jquery-3.1.1.min.js"></script>
<script type="application/javascript" src="/static/mapp/js/index.js"></script>
<link rel="stylesheet"  type="text/css" href="/static/mapp/css/style.css" />
```



##### 生产环境

两种方式

1. 通过html模版语句引入static标签 : 在html第一行引入{% load static from staticfiles%} 即可生效
2. 通过配置静态服务器引入 :配置static_root 等一系列部署操作



### 2.中间件

##### 介绍

Django底层插件,介于请求和响应间的处理

##### 本质

Python类

##### 方法列表

```python
__init__  #不需要传参数,在响应第一个请求时调用(用于启动中间件,类似构造对象的作用)

process_request(self,request) #分配url之前调用,每个请求都会调用,每个中间件都会调用,返回None或HttpResponse对象

process_view(self,request,view_func,view_args,view_kwargs) #调用view之前调用,每个请求都会调用,每个中间件都会调用,返回None或HttpResponse对象

process_template_response(self,request,response) #视图刚好执行完,找模版前执行该方法,每个请求都会调用,每个中间件都会调用,返回None或HttpResponse对象

process_response(self,request,response)#响应前,每个请求都会调用,每个中间件都会调用,返回HttpResponse对象

process_exception(self,request,exception) #视图抛出异常时调用,返回HttpResponse对象

```

##### 方法执行流程图

![Django中间件流程](https://ws1.sinaimg.cn/large/006tNbRwgy1fymsahfpkhj30u00u0dkl.jpg)

##### 自定义中间件

1. 自定义一个中间件包,包里写中间件的Python类
2. 所有中间件都需要继承Django的MiddlewareMixin类
3. settings.py   MIDDLEWARE中添加自定义中间件



### 3.上传文件

##### 配置

如果是存到数据库,则如下配置models

models中概字段标注为models.ImageField()



##### 上传流程

request.FILES接收文件

然后用各种方式处理即可,如oss中或者数据库中



### 4.分页

两个相关对象

1. Paginator
2. Page



##### Pageinator

创建对象格式 `Pageinator(列表,整数)`

1. 列表是所有记录
2. 整数是每页几条记录



属性列表

```python
count #列表总数
num_pages #页数
page_range #页码列表,从1开始
```

方法列表

```python
Page page(num) #num为页码,如果页码不存在,则抛出异常‘invalidPage’
```

异常列表

```python
invalidPage #num页码不存在时抛出,页面无效
PageNotAnInteger #num不是整数时抛出,页码不是整数
EmptyPage #num传入后有数据,但是页面无法获得数据时抛出
```



##### Page

创建对象:由Paginator.page(num)方法返回,不需要手动创建

属性列表

```python
object_list #当前页上所有数据
number #当前页页码
paginator #当前page对象绑定的paginator对象
```

方法列表

```python
has_next() #判断是否有下一页
has_previous() #判断是否有上一页
has_other_pages() #判断是否有上一页或下一页

next_page_number() #返回下一页的页码,页码不存在则抛出异常 invalidPage
previous_page_number() #返回上一页的页码,页码不存在则抛出异常 invalidPage

len() #当前页数据数量
```



##### 分页使用流程

views

```python
def cityPage(request,num):
	citylist=City.object.all()
	paginator=Paginator(citylist,6)
	page=paginator.page(num)
	return render(request,'myapp/citylist.html',{'citys':page})
```



html

```html
<h1>城市列表</h1>
{% for city in citys %}
	{{city.name}}--{{city.desc}}
{%endfor%}

<h1>城市分页按钮</h1>
{% for pageNumber in citys.paginator.page_range%} <!-- 循环页码列表 -->
	{% if pageNumber == citys.number%} <!-- 判断当前页链接失效 -->
		{{pageNumber}}
	{%else%}
		<a href='myapp/citypage/{{pageNumber}}'>{{pageNumber}}</a><!-- 判断其他页链接有效 -->
	{%endif%}
{%endfor%}
```



### 5.ajax

ajax后台视图响应时使用 JsonResponse即可,如下

```python
return JsonResponse({'data':list})
```

ajax其他都是通用流程,此处不再介绍



### 6.富文本

##### 安装tinymce

```shell
#在项目doc中安装tinymce才能生效
pip install django-tinymce
```



##### settings.py

```python
#引入tinymce应用
INSTALLED_APPS=[
    'tinymce'
]
#增加tinymce配置
TINYMCE_DEFAUTLE_CONFIG={ #tinymce配置
    'theme':'advanced', #所有功能全开
    'width':600,
    'height':400
}
```



##### 数据迁移

```shell
python3 manage.py makemigrations
python3 manage.py migrate
```



##### html

引入tinymce.js模块

```html
    <script type="text/javascript" src="/static/tiny_mce/tiny_mce.js"></script>
    <script type="text/javascript">
        tinyMCE.init({
            'mode':'textareas',
            'theme':'advanced',
            'width':800,
            'height':600,
        })
    </script>
    
<body>
    <form action="/cityDesc/input_text" method="post">
        <textarea name="str">请输入信息...</textarea>
        <input type="submit" value="提交"/>
    </form>
</body>
```



##### 配置view和url

普通流程,省略...



##### 站点配置

如果需要站点使用,注册到admin.py中即可使用站点管理富文本



### 7.celery

#### 概念

异步任务组件,可以异步处理数据,也可以定时任务分发,类似于java的Quartz

#### 目的

1. 异步处理耗时操作,优化用户体验,如用户注册时发送邮箱业务
2. 网站每隔一段时间同步数据(不需要用户刷新页面)



#### 组成

1. 任务(task) :python函数,业务逻辑
2. 队列(queue): 要执行的任务放在队列中
3. 工人(worker):执行任务
4. 代理(broker):任务调度,部署环境中使用redis





#### 基础配置

---

##### 安装

```
pip install celery
pip install celery-with-redis
pip install django-celery
```



##### Settings.py

```
#引入celery应用
INSTALLED_APPS=[
    ‘djcelery’
]

#初始化队列
import djcelery
djcelery.setup_loader() #初始化队列
BROKER_URL="redis://176.122.166.178:6379/0" #使用redis初始化代理
CELERY_IMPORTS=('myapp/task') #初始化导入任务
```



##### 数据迁移

```
python3 manage.py migrate
```



##### 工程目录创建celery.py文件

```
from __future__ import absolute_import

import os
from celery import Celery
from django.conf import settings

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'whthas_home.settings')

app = Celery('portal')

app.config_from_object('django.conf:settings')
app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)


@app.task(bind=True)
def debug_task(self):
    print('Request: {0!r}'.format(self.request))
```



##### init.py中导入celery

于mysql相同位置,全局导入

```
from .celery import app as celery_app
```



##### 配置流程总结

> 1. settings.py中配置celery应用以及初始化信息
> 2. celery.py创建配置文件
> 3. init.py中导入配置文件信息初始化



完成以上信息,celery基本配置完毕



#### 基础使用

---

##### task任务

应用创建task.py文件编写任务逻辑

```python
from celery import task
import time

@task
def mytask():
    print("开始Celery任务调度")
    print("发送邮件中。。。")
    time.sleep(5)
    print("发送邮件完成")
    print("结束Celery任务调度")
```



##### view使用

```python
from myapp.task import mytask

def celery(request):
    mytask.delay() #delay 添加到celery中执行，不会阻塞
    return render(request,"myapp/index2.html")
```



##### 开启服务

```shell
#命令行输入
python3 manage.py celery worker --loglevel=info
```



##### 使用流程总结

> 1. 创建的task相当于Quartz中的任务
> 2. python3.7不兼容celery 3.x版本,主要是异步属性的改变造成
> 3. delay()是简单的加入celery中异步执行,还有更多高级异步操作