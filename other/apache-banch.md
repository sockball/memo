* -n 总的请求数, 默认1 应大于-c的值, 否则报错(反正也不执行  
` -n 1000`
* -c 每次请求并发数, 默认1  
` -c 200`
* -t 测试进行的最大时间(s)  
` -t 20`
* -H 头部header参数 多个则写多个......  
` -H 'Accept:application/json'`
` -H 'x-app:SVPN-IOS' `
* -v 设置显示返回response的详细程度, 以下均为最小值 (即4包含3,2...  
` -v 4 #包含头信息`  
` -v 3 #包含status code`  
` -v 2 #包含警告及其他信息`
* -T 设置Content-Type头信息  
` -T application/json`  
` -T application/x-www-form-urlencoded`
* -p 包含请求body的文件（配合-T使用 且此时为POST请求）
 若-T 设置为application/json, 则文件的内容应为json字符串  
` -p data.txt #文件以ab所在目录为base目录`
* -P 应该是HTTP BASIC验证用  
`-P username:password`


* -w 以HTML table的形式输出结果(即用table代码包含返回信息) 但命令行下只显示源代码而没用??
* -V 查看ab版本
* -i 使用HEAD请求 (method: HEAD
<hr>
* <font color='sky-blue'>实例</font>

```
./ab \
-v 4 \
-n 1 \
-T application/json \
-H 'Accept:application/json' \
-H 'x-app:IOS-DDD' \
-H 'x-machine:machine-code' \
-H 'x-client-version:1.1' \
-H 'Authorization:Basic b3VoS1BpUm93dkRfRDB0WjBDdG1yT0E1dkJmUWdOb1I6' \
http://www.api.test.tk:8080/api/server/tree
```