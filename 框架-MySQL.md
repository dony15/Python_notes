# 框架-MySQL

[TOC]

### 1.概念



### 2.使用



```python
import pymysql

#创建链接
conn=pymysql.connect(host='176.122.166.178',port=3306,user='root',passwd='68835230',db='py_test',charset='utf8')

#创建游标
cursor=conn.cursor()
#sql
sql="select * from user"
#返回影响行数
row=cursor.execute(sql)
print(row) #2

#返回所有查找的信息
result=cursor.fetchall()
print(result) #((1, '谢永强', 18), (2, '谢大脚', 16))

#关闭游标
cursor.close()
#关闭连接
conn.close()
```

