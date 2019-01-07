# 中级-urllib-代理

[TOC]

### 1.Handler&opener基础

urllib高级功能版的处理方式,如:使用代理,处理cookie

##### 基础用法

```python
#省略...
hander=urllib.request.HTTPHandler()   
opener=urllib.request.build_opener(hander) 

response=opener.open(req) #使用方法同urlopen,包括参数选项(req为Request对象)

print(response.read())
```





### 2.Handler&opener代理

快代理 kuaidaili.com(可用度较低)

🌟西刺代理 https://www.xicidaili.com/(推荐)

##### 两种配置方式

1. 浏览器配置	(设置-->高级-->代理(mac版设置https安全网页代理即可))或 (运行代码生成txt文件,导入花刺工具筛选有效代理使用)
2. 代码配置 

```python
hander=urllib.request.ProxyHandler({'https':'119.101.116.206:9999'})
opener=urllib.request.build_opener(hander)

req=urllib.request.Request(url=url,headers=headers)

response=opener.open(req)
```



### 3.Cookie

抓包获取cookie后,放入headers头中进行爬取