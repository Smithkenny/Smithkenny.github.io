# docker冷门技巧


## 查看docker镜像的dockerfile

将`<IMAGE ID>`替换为待查看Dockerfile的镜像ID。

```bash
docker history <IMAGE ID> --format "{{.CreatedBy}}" --no-trunc |tac | awk '{if($3~/nop/){for(i=1;i<=3;i++){$i=""};print substr($0,4)}else{print "RUN",$0}}'
```

对比

```bash
[root@localhost ~]# docker history f1396d3fa7b5 --format "{{.CreatedBy}}" --no-trunc |tac | awk '{if($3~/nop/){for(i=1;i<=3;i++){$i=""};print substr($0,4)}else{print "RUN",$0}}'

ADD file:be998d04a8927e9c4b105995e3b9d6800ea798807389f7c5921c0f4774328e21 in /
CMD ["bash"]
COPY file:d3994e70cf156a17b1ad3dae5c75ed40c6d5c73a0d1b4c6b94620f1cd76b0943 in /etc/apt/sources.list
RUN /bin/sh -c apt update;apt install -y vim net-tools procps openssh-server python3.7
RUN /bin/sh -c sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;cp /usr/bin/python3.7 /usr/bin/python
CMD ["/bin/bash"]
```

原Dockerfile

```bash
FROM debian:10
COPY sources.list /etc/apt/sources.list
RUN apt update;apt install -y vim net-tools procps openssh-server python3.7
RUN sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config;cp /usr/bin/python3.7 /usr/bin/python
CMD ["/bin/bash"]
```

## 创建容器时指定主机名

加`--hostname`参数，`--network`指定网络。

```bash
docker run --name aa --hostname kafka_node -p 2181:2181 -v /home/htht/kafka/logs:/home/kafka/logs -v /home/htht/kafka/k-datas:/home/kafka/k-datas --network=my_net1 --ip 172.18.0.3  kafka:0.1
```


