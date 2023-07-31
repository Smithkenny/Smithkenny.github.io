# Crontab踩坑记


## Linux Crontab定时执行脚本出错，但手动执行脚本正常原因及解决方案

手动执行脚本可以执行成功，但用定时任务后却执行不成功。查看日志`tail -f /var/log/cron` 显示执行成功了。

```bash
2023-07-31T16:06:01.624003+08:00 localhost CROND[7853]: (root) CMD (/root/pidcheck.sh)
```

原因就在于，crontab没有去读环境变量，需要再脚本中手动引入环境变量。

```bash
#！bin/bash
source /etc/profile
source ~/.bash_profile

...
```

再执行时，问题解决。

原来就是因为crontab在执行脚本时没有读取环境变量，导致执行脚本失败。

