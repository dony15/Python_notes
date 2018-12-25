# 框架-Django(五)之中级model

[TOC]

### 1.概念

Django模型,抽象处理多种数据库,适配不同数据库的通用方法

ORM为中间层的方式处理数据

##### model描述

1. **主键:**Django会自动为表增加自增主键,`'pk'`,若果模型中自定义主键,则Django不会自动添加(表中默认一个主键)
2. **字段类型:**通过`from django.db import models`定义字段类型和限制,如`gName=models.IntegerField()`
3. **字段选项:**models属性的参数为**限制内容**或者**表单控件**(表单控件在站点中生效)



### 2.使用

##### 2-1.字段类型列表

```python
id=models.AutoField() #主键,自动增长
gName=models.CharField(max_length=20) #字符串类型,限制长度为20字符
gText=models.TextField() #一般超过4000使用,默认表单控件为Textarea
gInt=models.IntegerField() #整数类型
gFloat=models.DecimalField(max_digits=None,decimal_places=None) #浮点型 max_digits指格式化总位数,decimal_places指小数点后的位数,None为不做限制
isDelete = models.BooleanField(default=False) #布尔类型
gDate=models.DateField() #日期类型

createTime=models.DateField(auto_now_add=True) #创建时修改日期
lastTime=models.DateField(auto_now=True) #更新时修改日期

gFile=models.FileField() #文件类型
gImage=models.ImageField() #图片类型
...省略多种类型
```



##### 2-2.字段选项列表

```python
db_column='bodyNum' #设置数据库中字段名,如果没有设置,则默认使用属性名
unique=True #唯一约束
primary_key=True #主键约束
...省略多种选项
```



##### 2-3.关系列表

```python
ForeignKey #一对多,写在多端
ManyToManyField #多对多,写在两端
OneToOneField #一对一,写在任意一端

#关系操作方法
对象.模型类小写_set	#一访问多
对象.模型类小写	#一访问一
对象.外键属性_id #访问外键对应模版类(其他表)的id
```



##### 2-4.元选项

在模型类中定义Meta类,用于设置Meta信息

`db_table`:定义数据表名,如果未指定,则默认为`项目名小写_模型类名小写`

`ordering`:对象映射数据库字段排序设置,`id`为正序,`-id`为降序(注意,排序会降低性能)

```python
class 模型类:
	//models code
	
	class Meta:
		db_table='citys'
		ordering=['-id']
	
```



##### 2-5.模型成员

**类属性**:

`显式类属性`:显式定义属性,如:`sName=models.CharField(max_length=20)`

管理器:与数据库进行交互的接口,一个管理器类可以有多个自定义管理器

`object`:是Manager管理器的一个对象,作用是与数据库进行交互,当定义模型类时没有指定管理器,则Django自动创建一个`object`的管理器

自定义管理器:`obj=models.Manager()`,此时obj等价于`object`,并且此时object失效

自定义管理器类:向管理器添加方法,修改返回的原始查询集

```python
#自定义管理器类
class MyManager(models.Manager): #继承Manager管理器类
    def get_queryset(self): #重写get_queryset方法
        return super().get_queryset().filter(isDelete=False) #过滤掉已逻辑删除的数据
```



Manager查询集

```python
#1.过滤器形式处理查询集,多个条件可以逗号分隔,且的关系
get_queryset().filter(过滤条件kv,过滤条件kv)

#2.过滤器链式调用处理,且的关系
get_queryset().filter(过滤条件kv).filter(过滤条件kv)

#3.惰性查询

#4.直接查询
迭代/序列化/与if合用

#5.返回查询集的常用方法
all()
filter() #过滤符合条件数据
exclude() #过滤掉复合条件数据
order_by() #排序
values() #一个字典(一条记录,就是一个字典),多个字典返回一个列表

#6.返回单个数据的常用方法
get() #返回一条满足条件的记录,发现没有找到或者有多个满足条件对象时抛出异常
count() #返回满足条件的记录条数
first() #返回第一条满足条件的记录
last() #返回最后一条满足条件的记录
exists() #判断满足条件的记录是否存在

#7.限制查询集的常用方法(分页)
all()[0:4] #返回所有数据中的前三条,包前不包后,下标不可为负数,该方法可以用来做分页查询

#8.查询集的缓存
第一次查询后,Django会自动对该查询集进行缓存

#9.运算符
属性__gt #大于
属性__gte #大于等于
属性__lt #小于
属性__lte #小于等于

#10.聚合函数使用
aggregate(聚合函数)

#11.F对象
filter(属性1__gt=F(属性2))	#属性1>=属性2的条件 :可以使一个模型类中的字段进行比较(自查询)
filter(属性1__gt=F(属性2)+20) #F对象也可以使用运算符进行比较
#12.Q对象 主要用来处理or的字段关系
filter(Q(kv条件))  #满足kv条件用于匹配
filter(Q(kv条件1)|Q(kv条件2))  #满足kv条件1或者kv条件2用于匹配
filter(~Q(kv条件))  #满足kv条件取反后用于匹配
```



**创建模型对象**

```
在模型类中添加类方法(用于添加保存到数据库)
在管理器类中添加上述方法(用于添加保存到数据库)
```





