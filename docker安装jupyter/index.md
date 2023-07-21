# docker安装jupyter


# 安装jupyter

1、登录dockerhub查看需要的[Jupyter - Docker Official Images](https://hub.docker.com/r/jupyter/base-notebook/)。

2、下载jupyter镜像（以6.4.12为例）
`docker pull jupyter/base-notebook:notebook-6.4.12`

3、创建配置目录

```
mkdir -p /opt/jupyter/jovyan
mkdir -p /opt/jupyter/jovyan/.jupyter
chmod 777 -R /opt/jupyter/jovyan
```

4、启动jupyter服务

```
docker run --name vk-jupyter -d \
-p 8888:8888 \
-v /opt/jupyter/jovyan:/home/jovyan \
jupyter/base-notebook:notebook-6.4.12
```

以上命令：

- 命名容器为vk-jupyter，后台运行
- 映射宿主机8888端口到容器的8888端口
- 挂载宿主机目录/opt/jupyter/jovyan到容器目录/home/jovyan

更多启动命令参数可以参考[Jupyter Docker Stacks](https://jupyter-docker-stacks.readthedocs.io/en/latest/index.html)。

5、验证安装
`docker ps`，jupyter启动正常的话就可以看到vk-jupyter容器。

浏览器访问 [http://aliyunip:8888](http://阿里云ip:8888/) ，可以看到Jupyter登录页面。

6、登录
`docker exec -it vk-jupyter jupyter notebook list`
可以查看到登录需要的token，使用token即可登录进入jupyter编辑页面。

# 配置jupyter

1、设置密码

```
docker exec -it vk-jupyter jupyter notebook password
docker restart vk-jupyter
```

2、使用密码
浏览器访问 [http://aliyunip:8888](http://aliyunip:8888/)
此时使用自己设置的密码就可以访问jupyter了。

3、根目录
jupyter编辑器的默认根目录为 /home/jovyan ，对应宿主机目录 /opt/jupyter/jovyan ，创建的目录和文件都去这个路径下面去找。

# 配置Nginx

jupyter 使用了 websocket 协议，所以需要配置支持 websocket。
如果不配置的话，通过域名访问时会报错无法连接内核，也就无法运行python脚本。

```
server {
    listen 80;
    server_name jupyter.voidking.com;
    charset utf-8;
    location /{
        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;

        proxy_pass http://172.17.12.85:8888;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

# jupyter小技巧

## 执行bash

在代码框里输入叹号+bash命令，即可执行bash，例如：
`!ls -l`

## 登录进容器

```
docker exec -it vk-jupyter /bin/bash
docker exec --user root -it vk-jupyter /bin/bash
```

## 安装常用命令

```
docker exec --user root -it vk-jupyter /bin/bash
apt update
apt install curl
apt install unzip
```

