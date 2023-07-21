# ansible相关参考资料


## ansible模块官方手册

[模块](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html)

### 模块筛选

```bash
$ ansible-doc -l | grep 'copy'
vsphere_copy          Copy a file to a VMware datastore
win_copy              Copies files to remote locations on windows hosts
bigip_file_copy       Manage files in datastores on a BIG-IP
ec2_ami_copy          copies AMI between AWS regions, return new image id
win_robocopy          Synchronizes the contents of two directories using Robocopy
copy                  Copy files to remote locations
na_ontap_lun_copy     NetApp ONTAP copy LUNs
icx_copy              Transfer files from or to remote Ruckus ICX 7000 series switches
unarchive             Unpacks an archive after (optionally) copying it from the local machine
postgresql_copy       Copy data between a file/program and a PostgreSQL table
ec2_snapshot_copy     copies an EC2 snapshot and returns the new Snapshot ID
nxos_file_copy        Copy a file to a remote NXOS device
netapp_e_volume_copy  NetApp E-Series create volume copy pairs
```

详细的模块描述手册

```bash
ansible-doc copy
```

只包含模块参数用法的模块描述手册

```bash
ansible-doc -s copy
```

### 连接行为变量

[官网链接](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters)

常见的解释

|       Inventory变量名        |                             含义                             |
| :--------------------------: | :----------------------------------------------------------: |
|         ansible_host         |                  ansible连接节点时的IP地址                   |
|         ansible_port         |             连接对方的端口号，ssh连接时默认为22              |
|         ansible_user         | 连接对方主机时使用的主机名。不指定时，将使用执行ansible或ansible-playbook命令的用户 |
|       ansible_password       |                       连接时的用户密码                       |
|      ansible_connection      | 连接类型，有效值包括smart、ssh、paramiko、local、docker、winrm，默认为smart。smart表示智能选择ssh和paramiko，当SSH支持ControlPersist(即持久连接)时使用ssh，否则使用paramiko。local和docker是非基于ssh连接的方式，winrm是连接windows的插件 |
| ansible_ssh_private_key_file |               指定密钥认证ssh连接时的私钥文件                |
|   ansible_ssh_common_args    |              提供给ssh、sftp、scp命令的额外参数              |
|        ansible_become        |                       允许进行权限提升                       |
|    ansible_become_method     |      指定提升权限的方式，例如可使用sudo/su/runas等方式       |
|     ansible_become_user      |             提升为哪个用户的权限，默认提升为root             |
|   ansible_become_password    |                  提升为指定用户权限时的密码                  |

### yaml与json格式转换

[链接](https://yaml-online-parser.appspot.com/)

也可以用yq将yaml转换成json 格式。

### ansible关于lookup的插件

[参考](https://docs.ansible.com/ansible/latest/plugins/lookup.html#plugin-list)

### ansible支持的magic variables

[官网](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html#magic-variables)

### sh模式下没有ip  ifconfig 命令如何看IP？

```bash
hostname -I | awk ‘{print $1}’
```

### 没有telnet 工具如何测试端口？

```bash
(echo >/dev/tcp/172.18.0.1/6666) &>/dev/null && echo "open" || echo "closed"

开放 open 
未开放 closed
```

## ansible告警

```bash
[WARNING]: Platform linux on host node3 is using the discovered Python interpreter at /usr/bin/python3.7, but future installation of
another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
```

### 处理方式

关闭告警

```bash
vim /etc/ansible/ansible.cfg
[defaults]
interpreter_python=auto_silent
```

## debain ansible所有版本

```bash
https://packages.debian.org/buster-backports/all/ansible/download
```

## ansible rpm包所有版本

```bash
https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/
```

