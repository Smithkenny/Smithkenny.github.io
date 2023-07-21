# windows结束进程


## 所有的端口使用情况

```sh
netstat -ano
```

## 查看占用端口应用

```sh
netstat -ano | findstr "端口号"
```

## 通过id查找对应的进程名称

```sh
tasklist | findstr "进程id号" #列出进程名称
taskkill /f /t /im "进程id或者进程名称" #结束进程
```


