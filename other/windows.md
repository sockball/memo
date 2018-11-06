* schtasks定时任务...
```
# 创建 名为'dispatch' 每日17:30:00执行 某路径下的dispatch.bat文件 的定时任务
schtasks /create /st 17:30:00 /sc daily /tn 'dispatch' /tr path:\\dispatch.bat

# 删除 名为'dispatch'的任务
schtasks /delete /tn 'dispatch'
```