# 框架-Django(二)之入门view

[TOC]



### 1.概念

Django中,view是对url请求进行回应

视图就是一个Python的函数,在view.py中进行定义



### 2.使用

##### 定义视图view

`view.py`

```python
from django.shortcuts import render

# Create your views here.

from django.http import HttpResponse

def index(request):
    return HttpResponse("[打破玉龙飞采风，顿开金所走蛟龙]")
def desc(request,num):
    return HttpResponse("当前请求页面中，收到的数据为：%s"%(num))
```



##### 配置urls

`项目urls.py`用于分发url给指定应用中的url

```python
"""mydemo3 URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.1/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.conf.urls import url
from django.contrib import admin
from django.urls import  include

import myapp

urlpatterns = [
    url(r'^admin/',admin.site.urls),
    url(r'^',include('myapp.urls'))
    # path('admin/', admin.site.urls),
]

```



`应用urls.py`(默认没有创建,需手动创建),分发url给视图

```python
from django.conf.urls import url

from myapp import views

urlpatterns=[
    url(r'^$',views.index), #通过正则来判断接收的url
    url(r'^(\d+)/$',views.desc) #通过()来接收url中的数据
]
```



配置完毕

页面请求`localhost:8000`即可进入view中index函数

页面请求`localhost:8000/234/`即可进入view中desc函数,并将234作为参数传入



