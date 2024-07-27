MySQL 中有许多用于处理日期和时间的函数。以下是一些常用的 MySQL 日期函数：

1. **NOW()**：返回当前日期和时间。

2. **CURDATE()**：返回当前日期，不包括时间部分。

3. **CURTIME()**：返回当前时间，不包括日期部分。

4. **DATE()**：从日期时间值中提取日期部分。

5. **TIME()**：从日期时间值中提取时间部分。

6. **YEAR()**：从日期或日期时间值中提取年份部分。

7. **MONTH()**：从日期或日期时间值中提取月份部分。

8. **DAY()** 或 **DAYOFMONTH()**：从日期或日期时间值中提取天数部分。

9. **HOUR()**：从时间或日期时间值中提取小时部分。

10. **MINUTE()**：从时间或日期时间值中提取分钟部分。

11. **SECOND()**：从时间或日期时间值中提取秒数部分。

12. **DAYNAME()**：返回日期的星期几的名称。

13. **MONTHNAME()**：返回月份的名称。

14. **WEEKDAY()** 或 **DAYOFWEEK()**：返回日期是星期几，其中星期日为 1，星期一为 2，依此类推。

15. **DATE_FORMAT()**：将日期或日期时间值格式化为指定的字符串格式。

这些函数可以用于从日期时间值中提取特定部分、格式化日期时间字符串、比较日期时间值等各种操作。

 SELECT count(*)/16 FROM question_practice_detail  as t1 WHERE EXISTS (     SELECT 1     FROM question_practice_detail t2     WHERE t2.date = DATE_ADD(t1.date, INTERVAL 1 DAY) and t1.device_id =t2.device_id group by device_id);

select (SELECT count(DISTINCT device_id, question_id, date) FROM question_practice_detail AS t1 WHERE EXISTS (SELECT 1 FROM question_practice_detail t2 WHERE t2.date = DATE_ADD(t1.date, INTERVAL 1 DAY) AND t1.device_id = t2.device_id GROUP BY t2.device_id HAVING COUNT(*) > 0))/(select count(distinct device_id, date) from question_practice_detail) as avg_ret