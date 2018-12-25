# 框架-Django(三)之入门templates

[TOC]



### 1.概念

相当于h5页面的模子,template包与应用同级

使用时template中可以建子目录,子目录与应用名统一可以区别不同应用的模版



### 2.使用 

##### `settings.py`中配置目录

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages'
            ],
        },
    },
]
```



##### 模版语法

在h5模版中使用

```html
{{输出值}} #变量或者对象.变量
{%执行代码%} #可以用if else等python代码
```

如:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>班级信息</title>
</head>
<body>
<h1>班级信息</h1>
<ul>
    {%for grade in grades%}
    <li>
        <a href="#">{{grade.gName}}</a>
    </li>
    {%endfor%}
</ul>
</body>
</html>
```



