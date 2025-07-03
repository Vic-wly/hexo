title: mysql 日期函数简单应用
author: Laiyong Wang
tags:
  - mysql
categories: []
date: 2022-10-27 11:12:00
---
#### 常用日期函数
```
CURDATE() //当前系统的日期值
CURTIME() //当前系统的时间值
NOW()   //相当于CURDATE() . CURTIME()
UNIX_TIMESTAMP() //当前系统的时间戳
FROM_UNIXTIME(111111,format) //将时间戳转换成一定格式的日期
DATE_FORMAT(date,format) //根据 format 指定的格式显示 date 值,常用于返回具体日期是星期几，月份，年，啥的
DATE_SUB() //向日期减去指定的时间间隔
DATE_ADD() //向日期加上指定的时间间隔
```
#### 当天
```
//日期
CURDATE()
//or
FROM_UNIXTIME( UNIX_TIMESTAMP(), '%Y-%m-%d' )

时间戳
UNIX_TIMESTAMP(CURDATE())
//or
UNIX_TIMESTAMP(FROM_UNIXTIME( UNIX_TIMESTAMP(), '%Y-%m-%d' ))
```
#### 当月第一天 or 某一天
```
//日期
FROM_UNIXTIME( UNIX_TIMESTAMP(), '%Y-%m-1' )

//时间戳
UNIX_TIMESTAMP(FROM_UNIXTIME( UNIX_TIMESTAMP(), '%Y-%m-1' ))
```
#### 当年第一天(由上一条可得)
```
//日期
FROM_UNIXTIME( UNIX_TIMESTAMP(), '%Y-1-1' )

//时间戳
UNIX_TIMESTAMP(FROM_UNIXTIME( UNIX_TIMESTAMP(), '%Y-1-1' ))
```
#### 前 n 天时间（前 n 周时间改成 7n 天即可）
``` 
//日期
FROM_UNIXTIME( UNIX_TIMESTAMP( DATE_SUB( now(), INTERVAL 15 DAY )), "%Y-%m-%d" )
//or
FROM_UNIXTIME( UNIX_TIMESTAMP( DATE_ADD( now(), INTERVAL - 15 DAY )), "%Y-%m-%d" )

//时间戳
UNIX_TIMESTAMP( FROM_UNIXTIME( UNIX_TIMESTAMP( DATE_SUB( now(), INTERVAL 15 DAY )), "%Y-%m-%d" ))
//or
UNIX_TIMESTAMP( FROM_UNIXTIME( UNIX_TIMESTAMP( DATE_ADD( now(), INTERVAL -15 DAY )), "%Y-%m-%d" ))
```
#### 后 n 天时间（后 n 周时间改成 - (7 * n + 1) 天即可 ）
```
//日期
FROM_UNIXTIME( UNIX_TIMESTAMP( DATE_SUB( now(), INTERVAL -15 DAY )), "%Y-%m-%d" )

//时间戳
UNIX_TIMESTAMP( FROM_UNIXTIME( UNIX_TIMESTAMP( DATE_SUB( now(), INTERVAL -15 DAY )), "%Y-%m-%d" ))
```
#### 前几个月的某一天
```
//日期
date_format( date_sub( NOW(), INTERVAL 1 MONTH ), '%Y-%m-1' )
//时间戳
UNIX_TIMESTAMP(date_format( date_sub( NOW(), INTERVAL 1 MONTH ), '%Y-%m-1' )) 
```
#### 查询某一值（时间戳）是星期几(可扩展至月日每年第几周啥的，不想继续写了)
```
select 
CASE
		date_format( FROM_UNIXTIME( wlyTime, "%Y-%m-%d" ), "%w" ) 
		WHEN 1 THEN
		'星期一' 
		WHEN 2 THEN
		'星期二' 
		WHEN 3 THEN
		'星期三' 
		WHEN 4 THEN
		'星期四' 
		WHEN 5 THEN
		'星期五' 
		WHEN 6 THEN
		'星期六' 
		WHEN 7 THEN
		'星期天' ELSE "未知" 
	END AS week,
 from database_wly
 ```