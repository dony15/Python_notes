# 框架-Redis

[TOC]

### 1.概念



### 2.单入口使用



##### String类型存储

```python
import redis

r=redis.Redis(host='176.122.166.178',port=6379,decode_responses=True)

r.set('name','小黑')
r.pexpire('name',3000) #过期时间，毫秒

r.set('age',18)
r.expire('age',30) #过期时间，秒

#ex 过期时间(秒)  px 过期时间(毫秒) nx key不存在时，set才执行  xx key存在时，set才执行
r.set('lick','美食',ex=30,px=None,nx=False,xx=False)

result1=r.get('name')
result2=r.get('age')
result3=type(r.get('age'))
result4=r.get('lick')
print("%s , %s ,%s ,%s"%(result1,result2,result3,result4)) #小黑 , 18 ,<class 'str'>,美食
```



##### hash类型存储

```python
import redis

r=redis.Redis(host='176.122.166.178',port=6379,decode_responses=True)

r.hset('class1-1','谢大脚','学分：优秀')
r.hset('class1-1','王小二','学分：还行')
r.hset('class1-1','阿拉斯加','学分：🐷')

print(type(r.hkeys('class1-1'))) #<class 'list'>
print(r.hkeys('class1-1')) #['name', 'age', '谢大脚', '王小二', '阿拉斯加']
print(r.hget('class1-1','阿拉斯加')) #学分：🐷

#list批量处理
r.hmset('class1-2',{'谢永强':'学分：无解','王大锤':'学分：朽木'})

print(type(r.hkeys('class1-2'))) #<class 'list'>
print(r.hmget('class1-2',{'谢永强','王大锤'})) #['学分：朽木', '学分：无解']
```





### 3.redis连接池

```python
r_pool=redis.ConnectionPool(host='176.122.166.178',port=6379,decode_responses=True)
r=redis.Redis(connection_pool=r_pool)

r.hset('class1-1','谢大脚','学分：优秀')
r.hset('class1-1','王小二','学分：还行')
r.hset('class1-1','阿拉斯加','学分：🐷')
print(type(r.hkeys('class1-1'))) #<class 'list'>
print(r.hkeys('class1-1')) #['name', 'age', '谢大脚', '王小二', '阿拉斯加']
print(r.hget('class1-1','阿拉斯加')) #学分：🐷

#list批量处理
r.hmset('class1-2',{'谢永强':'学分：无解','王大锤':'学分：朽木'})

print(type(r.hkeys('class1-2'))) #<class 'list'>
print(r.hmget('class1-2',{'谢永强','王大锤'})) #['学分：朽木', '学分：无解']
```



