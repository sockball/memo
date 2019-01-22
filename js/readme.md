* 补前导0技巧
```
function format(num, n)
{
     return (Array(n).join(0) + num).slice(-n);
}
 ```

* `console.table({ 1: 2, 3: 4})` 

* 无限循环简写 `for (;;) {...}`

* js空位补0 `(Array(n).join('0') + num).slice(-n)` [参考](https://www.cnblogs.com/Jin-1007/archive/2012/04/13/2445531.html)

* js媒体查询
```
let media = window.matchMedia('(min-width:1200px)');
console.log(media.matches)
```