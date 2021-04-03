# MySQL插入当前时间、自动更新时间

##  MySQL 几种时间对比

| 类型| 字节 | 格式| 应用场景|是否支持设置系统默认值| 范围   |
| --------- | ---- | ------------------- | ----------------| ---------| ---- |
| date | 3 | YYYY-MM-DD          | 精确到天 | 不支持 | 1000-01-01/9999-12-31                   |
| time| 3 | HH : MM : SS            | 每天的时间 | 不支持| '-838:59:59'/'838:59:59'                |
| year  | 1 | YYYY  | 只要年份| 不支持              | 1901/2155                               |
| datetime  | 8    | YYYY- MM -DD HH : MM :SS | 精确到秒                             | MySQL 5.6.5支持      | 1000-01-01 00:00:00/9999-12-31 23:59:59 |
| timestamp | 4    | YYYYMMDD HHMMSS | 精确到秒或毫秒，时间戳，系统用于不同时区 | 支持                 |   	1970-01-01 00:00:00/2038<br>结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038年1月19日 凌晨 03:14:07|

## MySQL 插入时间  
1. NOW()函数以『YYYY-MM-DD HH: MM :SS』返回当前的日期时间，可以直接存到 DATETIME 字段中。
2. CURDATE()以『YYYY- MM -DD』的格式返回今天的日期，可以直接存到 DATE 字段中。
3. CURTIME()以『HH: MM :SS』的格式返回当前的时间，可以直接存到 TIME 字段中。

## 设置表字段默认 创建时间、更新时间

默认为创建时间(修改行后时间不会变化)
```sql
ALTER TABLE tab_name MODIFY COLUMN create_time TIMESTAMP 
NOT NULL 
DEFAULT CURRENT_TIMESTAMP

```
修改行自动更新时间
```sql
ALTER TABLE tab_name  MODIFY COLUMN update_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP 
ON UPDATE CURRENT_TIMESTAMP
```
## MySQL 日期计算
MySQL 为日期增加一个时间间隔：date_add()
```sql
set @dt = now();

select date_add(@dt, interval 1 day); -- add 1 day
select date_add(@dt, interval 1 hour); -- add 1 hour
select date_add(@dt, interval 1 minute); -- ...
select date_add(@dt, interval 1 second);
select date_add(@dt, interval 1 microsecond);
select date_add(@dt, interval 1 week);
select date_add(@dt, interval 1 month);
select date_add(@dt, interval 1 quarter);
select date_add(@dt, interval 1 year);

select date_add(@dt, interval -1 day); -- sub 1 day
```
MySQL 为日期减去一个时间间隔：date_sub()，和 date_add() 类似
```sql
mysql> select date_sub('1998-01-01 00:00:00', interval '1 1:1:1' day_second);

+----------------------------------------------------------------+
| date_sub('1998-01-01 00:00:00', interval '1 1:1:1' day_second) |
+----------------------------------------------------------------+
| 1997-12-30 22:58:59 |
+----------------------------------------------------------------+
```
两个日期相减 date1 - date2，返回天数。
```sql
select datediff('2008-08-08', '2008-08-01'); -- 7
select datediff('2008-08-01', '2008-08-08'); -- -7
```
两个日期相减 time1 - time2，返回 time 差值。
```sql
select timediff('2008-08-08 08:08:08', '2008-08-08 00:00:00'); -- 08:08:08
select timediff('08:08:08', '00:00:00'); -- 08:08:08
```
MySQL 时间戳（timestamp）转换、增、减函数：
```sql
select timestamp('2008-08-08'); -- 2008-08-08 00:00:00
按格式加
select timestamp('2008-08-08 08:00:00', '01:01:01'); -- 2008-08-08 09:01:01
按格式加
select timestamp('2008-08-08 08:00:00', '10 01:01:01'); -- 2008-08-18 09:01:01
加一天
select timestampadd(day, 1, '2008-08-08 08:00:00'); -- 2008-08-09 08:00:00
select date_add('2008-08-08 08:00:00', interval 1 day); -- 2008-08-09 08:00:00

timestampdiff(option,time1,time2)--  time2-time1
select timestampdiff(year,'2002-05-01','2001-01-01'); -- -1
select timestampdiff(day ,'2002-05-01','2001-01-01'); -- -485
select timestampdiff(hour,'2008-08-08 12:00:00','2008-08-08 00:00:00'); -- -12

select datediff('2008-08-08 12:00:00', '2008-08-01 00:00:00'); -- 7
时间戳计算时间差 > date计算时间差
```
## MySQL 时区（timezone）转换函数
```sql
convert_tz(dt,from_tz,to_tz)

select convert_tz('2020-03-20 22:15:32', '+00:00', '+08:00'); -- 2020-03-21 06:15:32
```

