* 范式简述  
第一范式： 每个字段为最小单位, 不可分割  
第二范式： 有主键, 其他字段依赖于主键保证唯一性  
第三范式： 消除传递依赖, 即消除冗余  
第四范式： 消除多值依赖, 即一对多的情况时分表处理

* 远程访问权限(8以前)  
    ```
    # *.* 对应 database.tablename 如 mysql.user、mysql.*
    # % 对应 IP 如 192.15.32.22
    GRANT ALL PRIVILEGES ON *.* TO 'username'@'%' IDENTIFIED BY  'password';
    FLUSH PRIVILEGES;
    ```
8.0以上版本
```
# 先创建用户
# WITH mysql_native_password为加密plugin附加选项 可省
CREATE USER 'user'@'%' IDENTIFIED WITH mysql_native_password BY 'password'

# 授权
GRANT ALL PRIVILEGES ON *.* TO 'user'@'%' WITH GRANT OPTION;

# 修改已有用户的加密plugin
ALTER USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY 'password'
```

* 批量修改数据库表名技巧
    ```
    # 表前缀为prev的改成db前缀
    SELECT CONCAT
    (
        'ALTER TABLE',
        table_name,
        ' RENAME TO db_',
        substring(table_name, 4),
        ';'
    )
    FROM information_schema.tables
    WHERE table_name LIKE 'prev%';
    ```

* 时间格式化 `created_at`字段为秒级时间戳
`DATE_FORMAT(FROM_UNIXTIME(created_at),'%Y-%m-%d %H:%i:%s')`

* JOIN字段名相同时使用USING简写  
`JOIN table AS b ON a.id = b.id`  
`JOIN table USING('id')`

* `order by` 与 `group by`同时使用时  
`order by`应在`group by`之后  
`group by`中出现的列应包含`order by`中的列  

* ERROR 1055, sql_mode启用only_full_group_by时, group by必须包含所有select的列..[参考](https://dev.mysql.com/doc/refman/5.7/en/group-by-handling.html)  
`select @@sql_mode`

* 8.0以上版本默认加密plugin为`caching_sha2_password` 可为远程用户设置plugin为`mysql_native_password`

* 8.0新增窗口函数与通用表表达式CTE[参考](https://www.cnblogs.com/wy123/archive/2018/03/14/8570011.html)  
使用窗口函数不能使用`*`列，必须显式指定列  
`func()over()`  
窗口函数示例：
    ```
    DROP TABLE IF EXISTS order_info;
    
    CREATE TABLE order_info
    (
        id INT PRIMARY KEY,
        username VARCHAR(50),
        amount INT,
        created_at DATETIME
    );
    
    INSERT INTO 
        order_info 
    VALUES
        (1,'u0001',100,'2018-1-1'),
        (2,'u0001',300,'2018-1-2'),
        (3,'u0001',300,'2018-1-2'),
        (4,'u0001',800,'2018-1-10'),
        (5,'u0001',900,'2018-1-20'),
        (6,'u0002',500,'2018-1-5'),
        (7,'u0002',600,'2018-1-6'),
        (8,'u0002',300,'2018-1-10'),
        (9,'u0002',800,'2018-1-16'),
        (10,'u0002',800,'2018-1-22')
    ```
    ```
    # row_number() 分组排序重新编号
    SELECT ROW_NUMBER()OVER(PARTITION BY username ORDER BY created_at desc) AS row_num,
         id,username,amount,create_at
    FROM order_info
    
    # rank() 相同数据编号相同 并且占用编号
    # dense_rank() 相同的编号不占用编号
    # avg(column), sum(column)等聚合函数 可计算累积值
    # ntitle(num) 将数据分为num组
    # first_value(column), last_value(column) 以当前列为基准, column的第一个值和最后一个值(每行得出值不同)
    # nth_value(column, n) 第n行后都返回第n行column的值, n之前为null
    # cume_dist(column) 小于等于当前列的column值的 列数/总列数
    # percent_rank() 当前column的 (rank -1) / (总列数 - 1)
    # lag(column, n) 前n行该column的值
    # lead(column, n) 后n行该column的值
    ```
CTE示例[参考](https://www.yiibai.com/mysql/cte.html)  
CTE的name后若跟上column_list,则应保证和内部查询列数相同, 并依次重命名其column...  
非递归CTE
```
WITH common_table_name(id, value) AS (
    SELECT 
        id, username
    FROM
        SSS
)
SELECT
    * 
FROM
    common_table_name 
```
递归CTE[参考](https://www.yiibai.com/mysql/recursive-cte.html) `WITH RECURSIVE`开头 以下查出当前日期至指定日期的值
```
# CAST函数 转换值为指定类型
# DATE_ADD函数 向日期添加指定的事件间隔
WITH RECURSIVE date_step(datetime_step) AS (
	SELECT
		CAST(sysdate() AS date)
	UNION ALL
	SELECT
		DATE_ADD(datetime_step, INTERVAL 1 day)
	FROM
		date_step
	WHERE
		datetime_step < '2018-10-07'
)
SELECT datetime_step FROM date_step
```
```
DROP TABLE IF EXISTS menu ;
CREATE TABLE menu (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name VARCHAR(50) NOT NULL,
	level TINYINT(1) DEFAULT 1,
	parent_id INT DEFAULT 0
);

INSERT INTO 
	menu (name, level, parent_id)
VALUES
	('多媒体管理', 1, 0),
	('视频', 2, 1),
	('图片', 2, 1),
	('音乐', 2, 1),
	('用户管理', 1, 0),
	('权限管理', 1, 0),
	('一级菜单', 1, 0),
	('1-1菜单', 2, 7),
	('1-2菜单', 2, 7),
	('1-3菜单', 2, 7),
	('1-1-1菜单', 3, 8),
	('1-1-2菜单', 3, 8),
	('1-2-1菜单', 3, 9),
	('1-2-2菜单', 3, 9),
	('1-3-1菜单', 3, 10),
	('1-3-2菜单', 3, 10)

# 查出一级菜单下所有子菜单
WITH RECURSIVE format_menu(id, name, level, parent_id) AS (
	SELECT
		id, name, level, parent_id
	FROM
		menu
	WHERE
		name LIKE '一级菜单'
    UNION ALL
        # 格式化输出
        # SELECT
        #     m.id, CONCAT(f.name, '  >  ', m.name), m.level, m.parent_id
		SELECT
			m.id, m.name, m.level, m.parent_id
		FROM
			menu AS m
		INNER JOIN
			format_menu AS f
		ON m.parent_id = f.id
)
SELECT
	*
FROM
	format_menu
```

* select查询时添加自定义字段且为固定值...  
`SELECT *, 1 AS point FROM table `(添加一列名为point值都为1的列)

* 5.7版本后子查询优化、子查询中先排序再分组失效...加上limit才有效  
参考[提问1](https://segmentfault.com/q/1010000011383702), [提问2](https://segmentfault.com/q/1010000011303563/a-1020000011310865)  

    [参考分析文章](https://www.cnblogs.com/ivictor/p/9281488.html) 
举例说明  
5.6之前的先排序后分组得到emp表中每个部门最高工资的雇员相关信息(目标行)

```
SELECT
    deptno,ename,sal 
FROM
    ( SELECT * FROM emp ORDER BY sal DESC ) t 
GROUP BY
    deptno;
```
5.7后该方法不适用... 除非在`ORDER BY`后加上`LIMIT`

[参考leetcode题目](https://leetcode-cn.com/problems/department-highest-salary/comments/)  
参考上面文章得出的该题解答方法...
```
# Write your MySQL query statement below
SELECT
    D.Name AS Department,
    E.Name AS Employee,
    E.Salary
FROM
    Employee E
INNER JOIN
    Department D
ON
    E.DepartmentId = D.id
INNER JOIN
(
    SELECT
        DepartmentId Id,
        max(Salary) Salary
    FROM
        Employee
    GROUP BY
        DepartmentId
) tmp
ON
    D.Id = tmp.Id
WHERE
    E.Salary = tmp.Salary

```

`ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`子句

* `IFNUll`函数  
`SELECT IFNULL((SELECT ...), NULL) AS result`

* `DATEDIFF`函数比较2个`DATE`类型的值, 结果为前一个日期减去后一个日期的天数  
`SELECT DATEDIFF('2018-08-07', '2018-08-08')`值为-1...

* `HAVING`中`COUNT()`也能去重 `HAVING COUNT(DISTINCT column)`

* [EXPLAIN使用分析](https://segmentfault.com/a/1190000008131735#articleHeader5)