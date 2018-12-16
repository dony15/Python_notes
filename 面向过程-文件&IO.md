# 面向过程-文件&IO

[TOC]

### 1.文件操作

```python
#文件打开关闭操作
open(strPath) #打开文件
open(strPath,'r') #按照权限模式打开文件
close() #关闭

#文件读操作
IO read(num) #读取文件指定字节数,默认无参表示全部
IO randline() #按照行读取,读取一行
IO randlines() #按照行读取,全部行,需要去掉末尾/n,如下:
for line in var.readlines():
    print(line.strip()) #读取配置文件时效果很好

#文件写操作
write() #写入文件

#内容定位
seek(0,0) #第二个参数表示指针偏移位置,0表示从文件头开始算(默认),1表示当前位置,2表示文章末尾位置
tell() #返回指针当前位置(字节位置)
    
```

### 2.文件操作模式

```
r #读,指针停留在开头,默认
w #写,如果存在则覆盖,如果不存在则创建
a #追加写,指针停留在末尾,如果存在则追加,如果不存在则创建

rb #二进制格式读,指针停留在开头,默认
wb #二进制格式写,如果存在则覆盖,如果不存在则创建
ab #二进制格式追加写,指针停留在末尾,如果存在则追加,如果不存在则创建

r+ #读写,指针停留在开头
w+ #读写,如果存在则覆盖,如果不存在则创建
a+ #读追加写,指针停留在末尾,如果存在则追加,如果不存在则创建

rb+ #二进制格式读写,指针停留在开头
wb+ #二进制格式读写,如果存在则覆盖,如果不存在则创建
ab+ #二进制格式读追加写,指针停留在末尾,如果存在则追加,如果不存在则创建
```



### 3.OS模块

```python
import os
os.name #查看系统类型 nt:win posix:linux
os.environ #查看系统环境变量
os.environ.get(path_name) #查看指定环境变量,大小写敏感
os.getcwd() #查看当前程序所在目录(该.py的目录)
os.listdir(path) #当前目录下所有直接子文件或子目录的名字

os.mkdir(path) #创建文件夹 相对和绝对路径皆可,如果path存在或中间目录不存在,则抛出异常
os.mkdirs(path) #创建文件夹 相对和绝对路径皆可,如果path存在,则抛出异常,建立中间不存在的目录
os.rmdir(path) #删除一个空目录(只能删除空目录)
os.stat(path) #查看文件或文件夹信息(大小,创建时间,修改时间,访问时间等)
os.rename(old,new) #修改文件或文件夹名
os.remove(filepath) #删除一个文件(只能删除文件)

os.system(command) #执行系统指令,如open 文件 (将会打开文件窗口)

os.path.join(path1,path2) #拼接路径
os.path.splitext(path) #得到文件后缀名
os.path.isdir(path) #判断是否为文件夹
os.path.isfile(path) #判断是否为文件
os.path.isexists(path)#判断路径是否存在,可判断文件或文件夹
os.path.getsize(path) #判断文件或文件夹大小,单位:字节
os.path.dirname(path) #返回文件或文件夹路径最后一个‘/’前面的部分
os.path.basename(path) #返回文件或文件夹路径最后一个‘/’后面的部分
os.path.split() #将路径根据最后一个‘/’分割为两部分


```



