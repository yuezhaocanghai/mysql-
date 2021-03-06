# MySQL日期时间函数

## datetime和timestamp 区别：

​		1.存储方式不一样：`timestamp` 它把客户端插入的时间从当前时区转化为UTC(世界标准时间)进行存储。查询时，将其有转化为客户端当前时区进行返回。而对于`datatime`,不做任何改变，基本上是原样输入和输出。

​		2.占用存储空间不同，`timestamp`储存占用4个字节，`datetime`储存占用8个字节。

​		3.可表示的时间范围不同。`timestamp`可表示范围：1970-01-01 00:00:00~2038-01-09 03:14:07,`datetime`支持的范围更广：1000-01-01 00:00:00~ 9999-12-31 23:59:59。

​		4.`timestamp`类型在默认情况下，insert、update 数据时，`timestamp` 列会自动以当前时间（current_timestamp）填充/更新。

​		5.索引速度不同。`timestamp` 更轻量，索引相对 `datetime` 更快。

## timestamp 类型字段特殊性：

​		`timestamp` 类型在自动初始化的时候，会自动设置成当前时间，会自动更新，默认设置为：

```mysql
'field' timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
```

如果不想要这样，可以将 `timestamp` 设置为默认为空或者设置具体的默认值。

## MySQL 获取日期时间函数：

### now()

​		`sysdate()` 日期时间函数和 `now()` 类似，不同之处在于：`now()` 在执行开始时值就得到了，`sysdate()` 在函数执行时动态得到值。

```mysql
mysql> select now(),sleep(3),now();
+---------------------+----------+---------------------+
| now()               | sleep(3) | now()               |
+---------------------+----------+---------------------+
| 2020-04-29 18:46:01 |        0 | 2020-04-29 18:46:01 |
+---------------------+----------+---------------------+
mysql> select sysdate(),sleep(3),sysdate();
+---------------------+----------+---------------------+
| sysdate()           | sleep(3) | sysdate()           |
+---------------------+----------+---------------------+
| 2020-04-29 18:46:38 |        0 | 2020-04-29 18:46:41 |
+---------------------+----------+---------------------+
```

​		可以看到虽然中途 sleep 3秒，但 now() 函数两次的时间值是相同的；sysdate() 函数两次得到的时间值相差3秒。

### curdata()

```mysql
mysql> select curdate();
+------------+
| curdate()  |
+------------+
| 2020-04-29 |
+------------+
```

`current_date()`，`current_date` 等同于 `curdate()`。

### curtime()

```mysql
mysql> select curtime();
+-----------+
| curtime() |
+-----------+
| 18:53:21  |
+-----------+
```

`current_time()`，`current_time`等同于`curtime()`。

### extract()

获取日期时间的各个部分：日期、时间、年、季度、月、日、小时、分钟、秒、微秒

```mysql
mysql> set @dt = '2020-04-29 18:56:30.123456';
mysql> select date(@dt);
+------------+
| date(@dt)  |
+------------+
| 2020-04-29 |
+------------+
mysql> select time(@dt);
+-----------------+
| time(@dt)       |
+-----------------+
| 18:56:30.123456 |
+-----------------+
mysql> select year(@dt);
+-----------+
| year(@dt) |
+-----------+
|      2020 |
+-----------+
mysql> select quarter(@dt);
+--------------+
| quarter(@dt) |
+--------------+
|            2 |
+--------------+
mysql> select month(@dt);
+------------+
| month(@dt) |
+------------+
|          4 |
+------------+
mysql> select week(@dt);
+-----------+
| week(@dt) |
+-----------+
|        17 |
+-----------+
mysql> select day(@dt);
+----------+
| day(@dt) |
+----------+
|       29 |
+----------+
mysql> select hour(@dt);
+-----------+
| hour(@dt) |
+-----------+
|        18 |
+-----------+
mysql> select minute(@dt);
+-------------+
| minute(@dt) |
+-------------+
|          56 |
+-------------+
mysql> select second(@dt);
+-------------+
| second(@dt) |
+-------------+
|          30 |
+-------------+
mysql> select microsecond(@dt);
+------------------+
| microsecond(@dt) |
+------------------+
|           123456 |
+------------------+
```

extract() 函数可以实现与上面类似的功能

```mysql
mysql> select extract(year from @dt);
+------------------------+
| extract(year from @dt) |
+------------------------+
|                   2020 |
+------------------------+
其他时间提取方式与年的方式类似；
mysql> select extract(year_month from @dt);
+------------------------------+
| extract(year_month from @dt) |
+------------------------------+
|                       202004 |
+------------------------------+
其他组合方式与上面类似；
```

### last_day()

last_day() 函数：返回月份中的最后一天；

```mysql
mysql> select last_day('2020-04-01');
+------------------------+
| last_day('2020-04-01') |
+------------------------+
| 2020-04-30             |
+------------------------+
mysql> select last_day('2020-07-08');
+------------------------+
| last_day('2020-07-08') |
+------------------------+
| 2020-07-31             |
+------------------------+
```

如果想得到当前月份有多少天，可以这样计算：

```mysql
mysql> select now(),day(last_day(now())) as days;
+---------------------+------+
| now()               | days |
+---------------------+------+
| 2020-04-29 21:14:25 |   30 |
+---------------------+------+
```

## MySQL日期时间计算函数

### date_add()

```mysql
mysql> set @dt = now();
mysql> select date_add(@dt, interval 1 day); -- add 1 day
+-------------------------------+
| date_add(@dt, interval 1 day) |
+-------------------------------+
| 2020-04-30 21:15:41           |
+-------------------------------+
```

`adddate()` ,`addtime()` 函数都可以用 `date_add()` 来替代。

```mysql
mysql> set @dt = now();
-- date_add() 函数为 @dt 增加了“1小时15分30秒”
mysql> select date_add(@dt,interval '01:15:30' hour_second);
+-----------------------------------------------+
| date_add(@dt,interval '01:15:30' hour_second) |
+-----------------------------------------------+
| 2020-04-29 22:36:51                           |
+-----------------------------------------------+
```

### date_sub()

date_sub() 函数与 date_add() 函数用法一致，不再赘述；

```mysql
mysql> select date_sub('2020-04-29',interval '1 2:2:2' day_second);
+------------------------------------------------------+
| date_sub('2020-04-29',interval '1 2:2:2' day_second) |
+------------------------------------------------------+
| 2020-04-27 21:57:58                                  |
+------------------------------------------------------+
```

`subdate()`，`subtime()` 函数均可以用date_sub() 代替。

### datediff()，timediff() 

日期、时间相减函数：`datediff(date1,date2)`，`timediff(time1,time2)`

MySQL `datediff(date1,date2)`:两个日期相减 date1-date2，返回天数。

```mysql
mysql> select datediff('2020-04-29','2020-01-01');
+-------------------------------------+
| datediff('2020-04-29','2020-01-01') |
+-------------------------------------+
|                                 119 |
+-------------------------------------+
mysql> select datediff('2020-01-01','2020-04-29');
+-------------------------------------+
| datediff('2020-01-01','2020-04-29') |
+-------------------------------------+
|                                -119 |
+-------------------------------------+
```

`timediff(time1,time2)`:两个日期相减 time1 - time2 ，返回 time 插值。

```mysql
mysql> select timediff('2020-04-29 21:34:30','2020-01-01 16:24:37');
+-------------------------------------------------------+
| timediff('2020-04-29 21:34:30','2020-01-01 16:24:37') |
+-------------------------------------------------------+
| 838:59:59                                             |
+-------------------------------------------------------+
```

注意：`timediff(time1,time2)`函数的两个参数类型必须相同。

### str_to_date()

(字符串转换为日期) 函数：`str_to_date(str,format)`

```mysql
mysql> select str_to_date('29/04/2020','%d/%m/%Y');
+--------------------------------------+
| str_to_date('29/04/2020','%d/%m/%Y') |
+--------------------------------------+
| 2020-04-29                           |
+--------------------------------------+
mysql> select str_to_date('2020-04-29','%Y-%m-%d');
+--------------------------------------+
| str_to_date('2020-04-29','%Y-%m-%d') |
+--------------------------------------+
| 2020-04-29                           |
+--------------------------------------+
```

### date_format()，time_format()

将日期、时间转换为字符串，是 `str_to_date()` 的逆转换。

```MYSQL
mysql> select date_format('2020-04-29 21:45:30','%W %M %Y');
+-----------------------------------------------+
| date_format('2020-04-29 21:45:30','%W %M %Y') |
+-----------------------------------------------+
| Wednesday April 2020                          |
+-----------------------------------------------+
mysql> select date_format('2020-04-29 21:45:30','%Y-%m-%d-%H-%i-%s');
+--------------------------------------------------------+
| date_format('2020-04-29 21:45:30','%Y-%m-%d-%H-%i-%s') |
+--------------------------------------------------------+
| 2020-04-29-21-45-30                                    |
+--------------------------------------------------------+
```

### MySQL时区（timezone）转换函数

`convert_tz(dt,from_tz,to_tz)`

```mysql
mysql> select convert_tz('2020-04-29 21:45:30','+08:00','+00:00');
+-----------------------------------------------------+
| convert_tz('2020-04-29 21:45:30','+08:00','+00:00') |
+-----------------------------------------------------+
| 2020-04-29 13:45:30                                 |
+-----------------------------------------------------+
```

时区转换也可以通过 `date_add`,`date_sub`,`timestampadd`来实现；

```mysql
mysql> select date_add('2020-04-29 21:45:30',interval -8 hour);
mysql> select date_sub('2020-04-29 21:45:30',interval 8 hour);
mysql> select timestampadd(hour,-8,'2020-04-29 21:45:30');
+--------------------------------------------------+
| date_add('2020-04-29 21:45:30',interval -8 hour) |
+--------------------------------------------------+
| 2020-04-29 13:45:30                              |
+--------------------------------------------------+
```

