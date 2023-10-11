# Greenplum-Db7容器方式安装


## 一、安装环境
- 提前安装任意版本docker
- 宿主机系统环境：centos7.7

## 二、docker安装

[安装过程](https://blog.csdn.net/BThinker/article/details/123358697)

## 三、安装脚本
- build.sh
- Dockerfile
- entrypoint.sh
- install.sh
- test.sh

`build.sh`脚本内容：

```sh
#!/bin/bash
############################################
# Function :  Docker镜像制作脚本
# Author : tang
# Date : 2021-07-04
#
# Usage: sh build.sh
#
############################################

docker build -t inrgihc/greenplum:7.0.0 .
```

`Dockerfile`脚本内容：

```sh
FROM rockylinux:9.2

# Greenplum的数据存放目录
ENV DATADIR=/data

# 文件准备
COPY install.sh /tmp/install.sh
COPY entrypoint.sh /entrypoint.sh

# 配置Aliyun的仓库(以安装libcgroup)
RUN yum install -y wget && wget -O /etc/yum.repos.d/Aliyun-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo

# 执行安装
RUN yum -y install passwd openssl openssh-server  openssh-clients &&\
    mkdir  /var/run/sshd/ && sed -i 's?#HostKey?HostKey?' /etc/ssh/sshd_config && \
    ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N '' && \
    echo -e '#!/bin/bash\n/usr/sbin/sshd -D &' > /var/run/sshd/sshd_start.sh && \
    chmod u+x /var/run/sshd/sshd_start.sh
RUN /var/run/sshd/sshd_start.sh && \
    sleep 10s && cd /tmp && sh /tmp/install.sh && \
    rm -rf /tmp/install.sh && rm -rf /tmp/files

# 暴露端口
EXPOSE 5432 22

USER  root

CMD  [ "sh" , "/entrypoint.sh"]
```

`entrypoint.sh` 脚本内容：

```sh
#!/bin/bash
############################################
# Function :  EntryPoint入口
# Author : tang
# Date : 2020-12-09
#
# Usage: sh entrypoint.sh
#
############################################

# 启动ssh服务
/var/run/sshd/sshd_start.sh && sleep 5s

# 设置gpadmin账号的环境变量
su - gpadmin -l -c "echo -e 'source /usr/local/greenplum-db/greenplum_path.sh' >> ~/.bashrc"
su - gpadmin -l -c "echo -e 'export COORDINATOR_DATA_DIRECTORY=$DATADIR/coordinator/gpseg-1/' >> ~/.bashrc"
su - gpadmin -l -c "echo -e 'export PGPORT=5432' >> ~/.bashrc"
su - gpadmin -l -c "echo -e 'export PGUSER=gpadmin' >> ~/.bashrc"
su - gpadmin -l -c "echo -e 'export PGDATABASE=postgres' >> ~/.bashrc"

if [ "`ls -A $DATADIR`" = "" ]; then
    # 创建数据库存放目录
    mkdir -p $DATADIR/coordinator
    mkdir -p $DATADIR/primary
    chown -R gpadmin:gpadmin $DATADIR

    # 启动数据库集群
    su - gpadmin -l -c "source ~/.bashrc;gpinitsystem -a -D -c /home/gpadmin/gpinitsystem_config_singlenode -h /home/gpadmin/gp_hosts_list"
    sleep 10s
    RET=`netstat -tuln | grep 5432 | wc -l`
    echo "!!!!!!Check server start:$RET"    
    su - gpadmin -l -c "source ~/.bashrc;psql -d postgres -U gpadmin -f /home/gpadmin/initdb_gpdb.sql"
    su - gpadmin -l -c "source ~/.bashrc;gpconfig -c log_statement -v none"
    su - gpadmin -l -c "source ~/.bashrc;gpconfig -c gp_enable_global_deadlock_detector -v on"
    su - gpadmin -l -c "echo \"host  all  all  0.0.0.0/0  password\" >> $DATADIR/coordinator/gpseg-1/pg_hba.conf"
    su - gpadmin -l -c "source ~/.bashrc && sleep 5s && gpstop -u && tail -f gpAdminLogs/*.log"
else
    su - gpadmin -l -c "source ~/.bashrc && gpstart -a && tail -f gpAdminLogs/*.log"
fi
```

`install.sh`脚本内容：

```sh
#!/bin/bash
############################################
# Function :  Greenplum单机一键安装脚本
# Author : tang
# Date : 2020-12-09
#
# Usage: sh install.sh
#
############################################

# GPDB的RPM包版本
GPDBVER=7.0.0
# 账号密码
PASSWORD=greenplum

# 日志等级
ERROR_MSG="[ERROR] "
INFO_MSG="[INFO] "

# 日志函数
function log() {
    TIME=$(date +"%Y-%m-%d %H:%M:%S")
    echo "$TIME $1"
}

# 利用yum安装依赖包函数
function package_install() {
  log "$INFO_MSG check command package : [ $1 ]"
  if ! rpm -qa | grep -q "^$1"; then
    yum install -y $1
    package_check_ok
  else
    log "$INFO_MSG command [ $1 ] already installed."
  fi
}

# 检查命令是否执行成功
function package_check_ok() {
  ret=$?
  if [ $ret != 0 ]; then
    log "$ERROR_MSG Install failed, error code is $ret, Check the error log."
    exit 1
  fi
}

function gpdb_install(){
    log "$INFO_MSG Start to install greenplum for single node."

    # 安装依赖包
    package=(wget iputils net-tools)
    for p in ${package[@]}; do
        package_install $p
    done

    # 安装readline(pgsql依赖)
    yum install -y readline-devel
    ln -s /usr/lib64/libreadline.so.8.1 /lib64/libreadline.so.7

    # 配置ping命令执行权限
    chmod u+s /bin/ping
 
    # 创建用户与用户组
    /usr/sbin/groupadd gpadmin
    /usr/sbin/useradd gpadmin -g gpadmin
    usermod -G gpadmin gpadmin
    echo "${PASSWORD}" | passwd --stdin gpadmin

    # ssh配置
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
    sed -i -r 's/^.*StrictHostKeyChecking\s+\w+/StrictHostKeyChecking no/' /etc/ssh/ssh_config
    sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
    systemctl restart sshd

    # gpadmin账号的免密配置
    su gpadmin -l -c "ssh-keygen -t rsa -f ~/.ssh/id_rsa -q -P \"\""
    su gpadmin -l -c "cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys"
    su gpadmin -l -c "chmod 600 ~/.ssh/authorized_keys" && \
    su gpadmin -l -c "ssh-keyscan -H localhost 2>/dev/null | grep rsa | awk '{print \"localhost \" \$2 \" \" \$3 }' >> ~/.ssh/known_hosts"

    # 下载并安装greenplum的RPM包
    wget --no-check-certificate "https://ghproxy.com/https://github.com/greenplum-db/gpdb/releases/download/$GPDBVER/open-source-greenplum-db-$GPDBVER-el8-x86_64.rpm" -P "/tmp/"
    yum install -y "/tmp/open-source-greenplum-db-$GPDBVER-el8-x86_64.rpm"
    rm -rf "/tmp/open-source-greenplum-db-$GPDBVER-el8-x86_64.rpm"

    # 安装openssl(postgres依赖openssl-1.1.1版本)
    wget --no-check-certificate https://www.openssl.org/source/old/1.1.1/openssl-1.1.1.tar.gz -P "/tmp/"
    cd /tmp && tar -zxvf openssl-1.1.1.tar.gz && cd openssl-1.1.1 && ./config --prefix=/usr/local/openssl && ./config -t 
    make && make install
    rm -f openssl-1.1.1.tar.gz && rm -rf openssl-1.1.1
    ln -s /usr/local/openssl/lib/libssl.so.1.1 /lib64/libssl.so.1.1
    ln -s /usr/local/openssl/lib/libcrypto.so.1.1 /lib64/libcrypto.so.1.1

    # 初始化集群并修改配置
    rm -f /home/gpadmin/gpinitsystem_config_singlenode

    cat > /home/gpadmin/gpinitsystem_config_singlenode << EOF
ARRAY_NAME="Greenplum Data Platform"
SEG_PREFIX=gpseg
PORT_BASE=6000
declare -a DATA_DIRECTORY=($DATADIR/primary $DATADIR/primary)
COORDINATOR_HOSTNAME=localhost
COORDINATOR_DIRECTORY=$DATADIR/coordinator
COORDINATOR_PORT=5432
TRUSTED_SHELL=ssh
CHECK_POINT_SEGMENTS=8
ENCODING=UNICODE
EOF

    cat > /home/gpadmin/gp_hosts_list << EOF
localhost
EOF

    cat > /home/gpadmin/initdb_gpdb.sql << EOF
ALTER ROLE "gpadmin" WITH PASSWORD '$PASSWORD';
EOF
    chown -R gpadmin:gpadmin /home/gpadmin

    log "$INFO_MSG Install single node Greenplum cluster success!"
}

# 安装操作
gpdb_install

```

`test.sh`脚本内容：

```sh
#!/bin/bash

rm -rf ./data && mkdir data
docker run -d --name greenplum -p 5432:5432 -v "$PWD"/data:/data  inrgihc/greenplum:7.0.0
```

## 四、部署过程

### 1.镜像制作

```sh
mkdir -p /tmpdisk/greendb7
cd /tmpdisk/greendb7
sh build.sh
```

### 2.镜像测试

```sh
cd /tmpdisk/greendb7
sh test.sh
```

### 3.使用

```sh
mkdir /tmpdisk/greendb7-data/gpdb
docker run -d --name greenplum -p 15432:5432 -v /tmpdisk/greendb7-data/gpdb:/data  inrgihc/greenplum:7.0.0
```

### 4.参数解释

说明：首次启动镜像时会初始化集群，需要耐心等待10~60秒左右，然后方可用客户端连接数据库。

| 参数名称          | 取值                    | 备注说明                                                  |
| ----------------- | ----------------------- | --------------------------------------------------------- |
| 软件安装路径      | /usr/local/greenplum-db | greenplum程序软件安装所在目录，目前无法定制配置           |
| 数据所在路径      | /data                   | greenplum数据库数据安装所在目录, 该参数可在打包时定制配置 |
| Greenplum超管账号 | gpadmin                 | 登录Greenplum数据库的超级管理员账号为gpadmin              |
| Greenplum超管密码 | greenplum               | 登录Greenplum数据库的超级管理员gpadmin的密码              |
| 数据库连接端口    | 5432                    | greenplum数据库master的连接端口号                         |

参考：[链接](https://gitee.com/inrgihc/greenplum_docker)




