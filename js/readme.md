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

* 快速转换整形... `value | 0`、`~~value`(11位数及以上不适用)

* 块的拖拽 ([参考](https://www.cnblogs.com/ghost-xyx/p/3833179.html)
```
dom {
    position: fixed;
    opacity: 0.85;
    left: 0px;
    bottom: 0px;
    z-index: 99999;
}

-------------

﻿function on (dom, eventName, callback)
{
    dom[`on${eventName}`] = callback
}

function off (dom, eventName)
{
    dom[`on${eventName}`] = null
}

function drag (dom)
{
    on(dom, 'mousedown', e => {
        const event = e || window.event
        // 仅左键可拖动
        if (event.button === 0)
        {
            // 记录mousedown时鼠标在dom内的坐标
            const domX = event.clientX - dom.offsetLeft
            const domY = event.clientY - dom.offsetTop
            on(document, 'mousemove', e => {
                const moveEvent = e || window.event
                dom.style.left = moveEvent.clientX - canvasX + 'px'
                dom.style.top = moveEvent.clientY - canvasY + 'px'
            })
        }
    })
    // 拖拽结束后解绑move事件
    on(dom, 'mouseup', () => {
        off(document, 'mousemove')
    })
}

```

* 滚动条回到顶部动画 ([参考](https://qishaoxuan.github.io/js_tricks/bom/#%E6%BB%9A%E5%8A%A8%E6%9D%A1%E5%9B%9E%E5%88%B0%E9%A1%B6%E9%83%A8%E5%8A%A8%E7%94%BB)
```
function scrollToTop()
{
  const scrollTop = document.documentElement.scrollTop || document.body.scrollTop
  // 值越小速度越快
  const speed = 10
  if (scrollTop > 0)
  {
    window.requestAnimationFrame(scrollToTop)
    window.scrollTo(0, scrollTop - scrollTop / speed)
  } 
  else 
  {
    window.cancelAnimationFrame(scrollToTop)
  }
}
```

* 随机数 [参考](http://www.cnblogs.com/starof/p/4988516.html)  

    ```
    // [0, MAX]
    Math.random() * (max + 1)
    
    // [MIN, MAX]
    Math.random() * (max + 1 - min) + min
    ```
* 自用`eslint rules`
    ```
    "indent": ["error", 4],
    "brace-style": 0,
    "comma-dangle": ["error", "only-multiline"]
    ```
* webpack部分配置示例
    ```
    devServer: {
        // @see https://webpack.js.org/configuration/dev-server/#devserverclientloglevel
        // eslint等的信息输出到浏览器的控制台上
        clientLogLevel: 'warning',
        proxy: {
            '/api': {
                target: 'http://www.show.tk',
                changeOrigin: true,
                secure: false,
                pathRewrite: {
                    '^/api': ''
                }
            }
        }
    }
    ```

* `Array.slice( int start [, int end]) : Array`  
返回从原数组中指定下标之间组成的新数组，参数可正可负，不改变原数组的值
    * 1个参数：返回该下标至结尾的所有项
        ```
        const arr = [1, 2, 3, 4]

        // [3, 4]
        arr.slice(2)

        // [2, 3, 4]
        arr.slice(-3)
        ```
    * 2个参数：返回`start`至`end - 1`的所有项
        ```
        const arr = [1, 2, 3, 4]

        // [3]
        arr.slice(2, 3)

        // [2, 3]
        arr.slice(-3, -1)
        ```

* `Array.splice(int start, int num, [, mixed insert, mixed insert ...]): Array`  
    删除数组指定数量的项，返回删除项组成的数组，`start`可正可负，`num`可为0(即不删除)  
    参数3之后指从`start`位置新增值
    ```
    const arr = [1, 2, 3, 4]

    // 返回[2, 3] 原数组变为[1, 4]
    arr.splice(1, 2)

    // 返回[] 原数组变为[1, 2, 4]=
    arr.splice(1, 0, 2)

    // 返回[2] 原数组变为[1, 200, 4]
    arr.splice(1, 1, 200)

    // 返回[200] 原数组变为[1, 400, 500, 4]
    arr.splice(1, 1, 400, 500)
    ```
* 拥有id的元素会创建全局变量，id即为变量名，控制台可直接使用

* Chrome支持对console.log的文字添加样式 e.g:
    ```
    var _log = console.log
    console.log = function() {
        _log.call(console, '%c' + [].slice.call(arguments).join(' '), 'color:transparent;text-shadow:0 0 2px rgba(0,0,0,.5);')
    }
    ```

* 使整个页面可编辑...`document.body.contentEditable='true'`

* `window.btoa()` -- base64编码（中文直接编码会报错）  
  `window.atob()` -- base64解码