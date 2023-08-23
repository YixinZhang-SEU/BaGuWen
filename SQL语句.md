# 高频SQL（基础版）

## 查询

> - 尽量不用`WHERE...OR...`
>
> - `!=`不包含值为`NULL`的情况，做值判断时要注意值为NULL的情况，可以用`ifnull`函数，`ifnull(x, 0)`：若`x`不为`NULL`则返回`x`的值，否则返回0。
>
> - UNION运算符用于组合两个或更多SELECT语句的结果集。
>
>   UNION中的每个SELECT语句必须具有相同的列数
>
>   - 这些列的数据类型必须兼容：类型不必完全相同，但是必须可以隐式转换。
>   - 每个SELECT语句中的列也必须以相同的顺序排列
>
>   UNION ALL 包含重复结果
>
> - `GROUP BY`会去重，8.0版本前会隐式排序
>
> - `char_length()`按字符计算，`length()`按字节计算，含有中文时应该选`char_length()`



## 连接

> - `LEFT JOIN`必须要加关键字`ON`限定匹配条件
> - 自连结  Table `JOIN` Table
> - `datediff(d2, d1)`表示d2与d1相差的天数
> - `WHERE` 子句只能指定行的条件，而不能指定组的条件，因此就有了 `HAVING` 子句，它用来指定组的条件。
> - `ROUND(XXX, 位数)` 保留小数；`IF(a, b, c)`若a成立，则输出b，否则输出c。



## 聚合函数

> - 可以直接在聚合函数里嵌套一个查询语句
>
>   ```mysql
>   SELECT r.contest_id, ROUND(COUNT(r.user_id)/(SELECT COUNT(*) FROM Users)*100, 2) AS percentage
>   FROM Register r
>   GROUP BY r.contest_id
>   ORDER BY percentage DESC, contest_id ASC
>   ```
> - `AVG(rating <3)` rating<3用于判断，小于3就是返回1，否则返回0。AVG求平均就跟把1和0求和再除以行数是一样的结果
> - `COUNT(条件）`不管记录是否满足条件表达式，只要非`NULL`就加1。所以应改为`SUM(state='approved')`,符合条件的就+1，或者`COUNT(IF(state='approved',1,null))`
> - 对符合条件的字段求和不能使用`SUM(条件)`，这样只是计数















