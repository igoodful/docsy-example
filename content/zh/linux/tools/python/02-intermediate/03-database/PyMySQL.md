---
title: PyMySQL
date: 2023-09-30
weight: 200
description: >
  PyMySQL 学习.
---
{{< alert >}}

{{< /alert >}}

##


```python
import mysql.connector
 
mydb = mysql.connector.connect(
  host="localhost",
  user="root",
  passwd="123456"
)
 
mycursor = mydb.cursor()
 
mycursor.execute("CREATE DATABASE runoob_db")





```





















