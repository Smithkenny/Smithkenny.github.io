# redhat6.3的yum源失效解决

## 备份原repo

```shell
mv /etc/yum.repos.d/rhel-source.repo /etc/yum.repos.d/rhel-source.repo-old
```

## 新建repo文件

vi /etc/yum.repos.d/rhel-source.repo

```shell
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-6.5 - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.5/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#released updates
[updates]
name=CentOS-6.5 - Updates - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.5/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#additional packages that may be useful
[extras]
name=CentOS-6.5 - Extras - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.5/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#additional packages that extend functionality of existing packages
[centos-vaultplus]
name=CentOS-6.5 - Plus - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.5/centos-vaultplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6

#contrib - packages by Centos Users
[contrib]
name=CentOS-6.5 - Contrib - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.5/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6
```

## 清除缓存并重建缓存

```shell
yum clean all && yum makecache
```


