# 框架-Django(一)之入门model&admin

[TOC]



### 1.概念

python的WEB框架,基于mtv模式开发

个人认为Django最大的优势是后台的集成管理



### 2.目录结构

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyf84jvueej30mw0jqmza.jpg)



`manage.py` 命令行工具,多种方式对Django项目进行交互

`__init__.py`告诉python,这个目录应该被看作是一个包

`setting.py`项目配置文件(全局)

`urls.py`路由,项目的url声明

`wsgi.py`Web服务器网关接口（Python Web Server Gateway Interface，缩写为**WSGI**）是为Python语言定义的Web服务器和Web应用程序或框架之间的一种简单而通用的接口。



### 3.manage.py

```python
#manage.py 交互指令列表
python manage.py startapp 应用名 #创建应用
python manage.py makemigrations #生成数据迁移
python manage.py migrate #数据迁移到数据库
python manage.py shell #进入python manage shell交互操作
python manage.py runserver ip:port #启动轻量级web服务
python manage.py createsuperuser #创建管理员用户
```



### 4.使用

##### 配置数据库-MySQL

`__init__.py`

```python
import pymysql
pymysql.install_as_MySQLdb()
```



`settings.py-->DATABASES`

```python

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql', #使用SQL的选择,这里替换为mysql
        'NAME':'py_test01',
        'USER':'root',
        'PASSWORD':'68835230',
        'HOST':'192.168.31.64',
        'PORT':'3306'
    }
}
```



##### 创建应用

```sh
#终端窗口执行
python manage.py startapp myapp
```

通过终端生成应用代码模版

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fyfbidwnk5j30gw0ko40m.jpg)



##### 应用目录

`admin.py` 站点配置

`apps.py`

`models.py`数据模型

`views.py`视图



##### 配置激活应用

`settings.py`

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp' #在此添加创建的app名即可
]

#汉化
LANGUAGE_CODE = 'zh-Hans'
# LANGUAGE_CODE = 'en-us'

TIME_ZONE = 'Asia/Shanghai'
# TIME_ZONE = 'UTC'
```





`models.py`

python模型中可以不声明定义主键,主键在生成时会自动生成并增加

一个模型类,对应Mysql中的一个表

**注意:此处`__str__`返回简练的字段是为了admin后台管理时观看关联更加方便**

```python
from django.db import models

# Create your models here.

class Grades(models.Model):
    gName=models.CharField(max_length=20)
    gDate=models.DateField()
    gBodyNum=models.IntegerField()
    gGirlNum=models.IntegerField()
    isDelete=models.BooleanField(default=False)

    def __str__(self):
        # return "班级名：%s,班级创建日期：%s,男生数：%d，女生数：%d"%(self.gName,self.gDate,self.gBodyNum,self.gGirlNum)
        return self.gName

class Students(models.Model):
    sName=models.CharField(max_length=20)
    sGender=models.BooleanField(default=True) #True 男 | False 女
    sAge=models.IntegerField()
    sContend=models.CharField(max_length=20)
    isDelete = models.BooleanField(default=False)
    sgrade=models.ForeignKey('Grades',on_delete=models.CASCADE) #外键关联Grades，完成一对多的实现

    def __str__(self):
        # return "学生名：%s,性别：%s，年龄：%d，介绍：%s"%(self.sName,self.sGender,self.sAge,self.sContend)
        return self.sName
```



生成迁移文件

`终端执行 python manage.py makemigrations`

执行迁移数据到数据库中(创建表完成)

`终端执行 python manage.py migrate`



模拟调用models

`python manage.py shell`

```python
#增
对象.save() #保存数据到数据库中,同一个引用模型多次保存相当于修改
#删
对象.delete()
#查
类名.objects.all() #获取表中全部数据
类名.objects.get(pk=1)  #获取表中主键=1的数据

#关联存储
#方式1
关联对象.sgrad=被关联对象
关联对象.save()
#方式2 **推荐**
被关联对象.关联对象_set.create(被关联对象属性,如 sName='谢永强',sAge=18)


#关联查询
被关联对象.关联对象_set.all() 
```



### 5.启动服务器

这是一个纯python编写的轻量级web服务器,仅仅在开发时使用

```python
python manage.py runserver ip:port  #ip:port可以省略,默认本机:8000
```



### 6.站点管理

##### 1.`settings.py`配置`admin应用`

```python
INSTALLED_APPS = [
    'django.contrib.admin', #配置admin应用
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp'
]
```



```python
python manage.py createsuperuser #创建管理员
http://localhost:8000/admin/
```



##### 2.`admin.py`

修改该文件进行模型注册

```python
from django.contrib import admin

# Register your models here.

from .models import Grades,Students

#注册方法1
admin.site.register(Grades)
admin.site.register(Students)

#注册方法2装饰器
@admin.register(Grades)
#自定义管理页面类
```



##### 3.自定义管理页面



**`admin.ModelAdmin`: 视图管理对象**

---

`list_display`:页面中的显示字段

`list_filter`:过滤器,设置过滤字段

`search_fields`:设置搜索字段

`list_per_page`:分页字段数量



`fields`:设置属性顺序

`fieldsets`:给属性分组

**注意fields与fieldsets不可同时使用**



`inlines`:添加行,添加的是关联对象



**`admin.TabularInline`:关联对象**

----------------

`model`指定数据模型

`extra`指定添加数据模型的数量



**函数解决bool类型问题**

---

函数可以替代字段显示,如bool类型的显示

通过`函数名.short_description`可以设置该函数字段所在的列名



**`actions`动作属性**

---

`actions_on_top = True` #在顶部显示动作执行窗口

`actions_on_bottom = True` #在底部显示动作执行窗口

---



```python
from django.contrib import admin

# Register your models here.

from .models import Grades,Students


class GradesAdmin(admin.ModelAdmin):
    #列表页属性
    list_display = ['pk','gName','gDate','gBodyNum','gGirlNum','isDelete']
    list_filter = ['gName']
    list_per_page = 5
    search_fields = ['gName']

    #添加.修改页属性
    # fields = ['gName','gDate','gBodyNum','gGirlNum','isDelete']
    fieldsets = [
        ("基础字段", {"fields": ['gName', 'gDate']}),
        ("数量字段",{"fields":['gBodyNum','gGirlNum']}),
        ("系统字段",{"fields":['isDelete']})
    ]
admin.site.register(Grades,GradesAdmin)

class StudentsAdmin(admin.ModelAdmin):

    #函数显示
    def sGender(self):
        if self.sGender:
            return "男"
        else:
            return "女"

    def isDelete(self):
        if self.isDelete:
            return "不可用"
        else:
            return "可用"
    sGender.short_description = "性别"
    isDelete.short_description="是否可用"

    #列表页属性
    list_display = ['pk','sName',sGender,'sAge','sContend',isDelete,'sgrade']
    list_filter = ['sName']
    list_per_page = 5
    search_fields = ['sName']

    #添加，修改属性
    fieldsets = [
        ("学生基本信息",{"fields":['sName','sGender','sAge']}),
        ("学生介绍信息",{"fields":['sContend']}),
        ("所在班级信息",{"fields":['sgrade']}),
        ("系统字段",{"fields":['isDelete']})
    ]

    #动作属性
    actions_on_top = True #在顶部显示动作窗口

admin.site.register(Students,StudentsAdmin)
```



