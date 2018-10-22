* 范式简述  
第一范式： 每个字段为最小单位, 不可分割  
第二范式： 有主键, 其他字段依赖于主键保证唯一性  
第三范式： 消除传递依赖, 即消除冗余  
第四范式： 消除多值依赖, 即一对多的情况时分表处理

* 远程访问权限
```
# *.* 对应 database.tablename 如 mysql.user、mysql.*
# % 对应 IP 如 192.15.32.22
GRANT ALL PRIVILEGES ON *.* TO 'username'@'%' IDENTIFIED BY  'password';
FLUSH PRIVILEGES;
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