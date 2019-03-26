### 分组求最值测试

环境：`MYSQL 8.0+`  
数据来自：[https://github.com/datacharmer/test_db](https://github.com/datacharmer/test_db)  
测试`table`：`salaries`  
测试数据：`2,838,426`

---

* `MYSQL 5.6`常用方式改写 ( ≈ 3s 子句不加LIMIT结果秒出，但是结果实则非期望值... )
    ```
    SELECT 
        *
    FROM
    (
        SELECT
            emp_no,
            salary,
            from_date,
            to_date
        FROM
            salaries
        ORDER BY
            emp_no,
            salary DESC
        LIMIT
            9999999
    ) tmp
    GROUP BY
        emp_no
    LIMIT
        10
    
    ```
---

* 最佳通用方式  ( ≈ 0.5s )
    ```
    SELECT
        s.emp_no,
        s.salary,
        s.from_date,
        s.to_date
    FROM
        salaries s,
        (
            SELECT
                emp_no,
                max(salary) max_salary
            FROM
                salaries
            GROUP BY
                emp_no
        ) tmp
    WHERE
        s.emp_no = tmp.emp_no
    AND
        s.salary = tmp.max_salary 
    LIMIT
        10
    
    ```

---

* 秒出方案...  
实则受LIMIT影响，LIMIT越大耗时越大...  
LIMIT在`15,000`左右约为0.5s
    ```
    SELECT
            s.emp_no,
            s.salary,
            s.from_date,
            s.to_date
    FROM
        salaries s
    WHERE
        salary =
        (
            SELECT
                MAX(salary)
            FROM
                salaries
            WHERE
                emp_no = s.emp_no
        )
    LIMIT
        10
    ```

---

* `RANK()` ( ≈ 2.7s )
    ```
    SELECT
        emp_no,
        salary,
        from_date,
        to_date
    FROM
    (
        SELECT
            *,
            RANK() OVER(PARTITION BY emp_no ORDER BY salary DESC) row_num
        FROM
            salaries
    ) tmp
    WHERE
        row_num < 2
    LIMIT
        10
    ```

---

* `LAST_VALUE() - 1` ( ≈ 5.3s )
    ```
    SELECT
        emp_no,
        salary,
        from_date,
        to_date
    FROM
    (
        SELECT
            *,
            LAST_VALUE(salary) OVER(PARTITION BY emp_no ORDER BY salary) max_salary
        FROM
            salaries
    ) tmp
    WHERE
        salary = max_salary
    LIMIT
        10
    ```

---

* `LAST_VALUE() - 2` ( 直接卡死... )
    ```
    SELECT
        DISTINCT emp_no,
        LAST_VALUE(s.salary) OVER(PARTITION BY emp_no ORDER BY s.salary) salary,
        LAST_VALUE(from_date) OVER(PARTITION BY emp_no ORDER BY s.salary) from_date,
        LAST_VALUE(to_date) OVER(PARTITION BY emp_no ORDER BY s.salary) to_date
    FROM
        salaries s
    LIMIT
        10
    
    ```