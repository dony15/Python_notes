# 基础-urllib

[TOC]



### 1.概念

python的爬虫模块,python3主要包含如下两个模块

```python
urllib.request
urllib.pase
```



### 2.urllib.request库

```
import urllib.request
```



##### 忽略证书

```
import ssl
ssl._create_default_https_context=ssl._create_unverified_context #默认忽略https证书
```



##### urlopen

访问url响应response

````python
#语法
#只用url是get请求,数据在url中,data是post请求时使用
response=urllib.request.urlopen(url=xxx [,data=xxx]) 
#可传入模拟请求对象,进行伪装请求
response=urllib.request.urlopen(request [,data=xxx]) 

#-------------------------------
url="http://www.baidu.com"
response=urllib.request.urlopen(url)
print(response) #返回HttpResponse对象

with open('baidu2.html','wb+') as re:  #写入到本地文件
    re.write(response.read()) 
````



##### response方法列表

```python
read() #读取
encoding() #指定编码,默认utf8,字符串转字节
decoding() #指定编码,默认utf8,字节转字符串

geturl() #根据相应内容,获取请求的url,如:http://www.baidu.com
getheaders() #获取头信息,默认列表套元组的格式,可用dict()转为字典类型
getcode() #获取相应状态码
readlines() #按行读取,返回字节类型
```



##### urlretrieve

直接将数据写到本地文件中

```python
#语法
#urlretrieve无法直接使用Request对象,无法如urlopen那样模拟UA信息,但是方便存储
response=urllib.request.urlretrieve(url,filePath)

#-------------------------------
url="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1546756071153&di=bf6f3153a98d1c651162792e239437a6&imgtype=0&src=http%3A%2F%2Fpic29.nipic.com%2F20130527%2F7447430_052631769000_2.jpg"

urllib.request.urlretrieve(url,'img3.jpg') #第一个参数为url,第二个为保存路径
```



### 3.urllib.parse库

函数模块如下

```python
import urllib.parse

quote(url) #url编码,中文转为%xxx,转为url规则,请求一般需要编码后进行
unquote(url) #url解码,将url规则编码转为原始内容,%xxx转为中文
urlencode(dict) #将dict参数转为url参数格式并进行编码,返回如: ?name=%xxx&age=18&like=%xxx
```



##### 示例代码

```python
url="https://www.baidu.com/s?"
search=input('请输入搜索内容:')
data={	#参数
    'ie': 'utf-8',
    'wd':search
}

url+=urllib.parse.urlencode(data)
fileName=search+".html" #文件名为搜索关键字
#访问并存储
urllib.request.urlretrieve(url,fileName)
print("搜索完成")
```



### 4.Request对象

通过构建Request对象来模拟浏览器请求(反爬)

需要模拟浏览器凭证,可搜索UA大全



##### 构建请求对象爬取流程

```python
#语法
#用于构建请求对象,可以模拟头信息等,伪装请求,与urlopen结合使用
response=urllib.request.Request(url=xxx [,headers=xxx][,**kwargs]) 

#-------------------------------
# 请求url
url = "https://www.baidu.com/s?"

# 模拟请求头凭证
headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36'

}

#搜索关键字
search = input('请输入搜索内容:')

#请求参数
data = {
    'ie': 'utf-8',
    'wd': search
}
url += urllib.parse.urlencode(data)

#文件名
fileName = search + ".html"

# 构建请求对象
req = urllib.request.Request(url=url, headers=headers)

# 通过Request对象,发起请求
response=urllib.request.urlopen(req)

#读取结果,或存储
print(response.read().decode())
```



##### 5.POST爬取

> post的请求方式不同于get,需要将数据存放在from Data中,并且进行编码



```python

# 请求url
url = "https://fanyi.baidu.com/sug"

# 模拟请求头凭证
headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36'

}

search = input('请输入搜索内容:')
from_data = {
    'kw':search
}

from_data=urllib.parse.urlencode(from_data).encode() #注意,post请求数据需要编码转为字节,否则异常

fileName = search + ".html"

# 构建请求对象
req = urllib.request.Request(url=url, headers=headers)

# 发起POST请求
response=urllib.request.urlopen(req,data=from_data)
print(response.read().decode())
print("搜索完成")
```

