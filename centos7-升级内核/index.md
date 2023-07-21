# CentOS7 升级内核

# CentOS 7 升级内核

```shell
uname -r
# 查看内核
yum install -y https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
yum  --disablerepo="*"  --enablerepo="elrepo-kernel"  list  available
yum  --enablerepo=elrepo-kernel  install  -y  kernel-lt
awk -F\' '$1=="menuentry " {print i++ " : " $2}' /boot/grub2/grub.cfg
yum install -y grub2-pc
grub2-set-default 0
vim /etc/default/grub
# 修改 GRUB_DEFAULT=0
grub2-mkconfig -o /boot/grub2/grub.cfg
reboot
# 重启
uname -r
# 查看内核

############################## 以下可选 ##############################
yum remove kernel-tools-libs.x86_64 kernel-tools.x86_64
# 删除版本工具包
yum --disablerepo=\* --enablerepo=elrepo-kernel install -y kernel-lt-tools.x86_64
# 安装新版本工具包
rpm -qa | grep kernel
# 查看已安装内核
```

