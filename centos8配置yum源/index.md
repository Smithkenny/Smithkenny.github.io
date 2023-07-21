# CentOS8配置yum源

# CentOS8配置yum源

将源文件备份

```python
cd /etc/yum.repos.d/ && mkdir backup && mv *repo backup/
```

下载yum源文件

```python
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
```

修改yum源里面的地址

```python
sed -i -e "s|mirrors.cloud.aliyuncs.com|mirrors.aliyun.com|g " /etc/yum.repos.d/CentOS-*
sed -i -e "s|releasever|releasever-stream|g" /etc/yum.repos.d/CentOS-*
```

重新生成缓存

```python
yum clean all && yum makecache
```


