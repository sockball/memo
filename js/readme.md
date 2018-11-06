* 补前导0技巧
```
function format(num, n)
{
     return (Array(n).join(0) + num).slice(-n);
}
 ```

* `console.table({ 1: 2, 3: 4})` 

* 无限循环简写 `for (;;) {...}`