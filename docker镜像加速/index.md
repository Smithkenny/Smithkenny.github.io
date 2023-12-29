# Docker镜像加速


## docker镜像加速
创建`/etc/docker/daemon.json`文件
```sh
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## docker hub 国内镜像加速列表

|                          镜像加速器                          |              镜像加速器地址              |   专属加速器？   |                其它加速？                 |
| :----------------------------------------------------------: | :--------------------------------------: | :--------------: | :---------------------------------------: |
| [DaoCloud 镜像站](https://github.com/DaoCloud/public-image-mirror) |       https://docker.m.daocloud.io       |                  | Docker Hub、GCR、K8S、GHCR、Quay、NVCR 等 |
| [Azure 中国镜像](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy) |        https://dockerhub.azk8s.cn        |   仅供内部访问   |           Docker Hub、GCR、Quay           |
| [科大镜像站](https://mirrors.ustc.edu.cn/help/dockerhub.html) |    https://docker.mirrors.ustc.edu.cn    |   仅供内部访问   |           Docker Hub、GCR、Quay           |
|           [阿里云](https://cr.console.aliyun.com/)           | https://<your_code>.mirror.aliyuncs.com  | 需登录，系统分配 |                Docker Hub                 |
|              [网易云](https://c.163yun.com/hub)              |       https://hub-mirror.c.163.com       |                  |                Docker Hub                 |
| [腾讯云](https://cloud.tencent.com/document/product/457/9113) |    https://mirror.ccs.tencentyun.com     |   仅供内部访问   |                Docker Hub                 |
|         [Docker 镜像代理](https://dockerproxy.com/)          |         https://dockerproxy.com          |                  |        Docker Hub、GCR、K8S、GHCR         |
| [百度云](https://cloud.baidu.com/doc/CCE/s/Yjxppt74z#%E4%BD%BF%E7%94%A8dockerhub%E5%8A%A0%E9%80%9F%E5%99%A8) |       https://mirror.baidubce.com        |                  |                Docker Hub                 |
|     [南京大学镜像站](https://doc.nju.edu.cn/books/35f4a)     |        https://docker.nju.edu.cn         |                  |   Docker Hub、GCR、GHCR、Quay、NVCR 等    |
|     [上海交大镜像站](https://mirrors.sjtug.sjtu.edu.cn/)     | https://docker.mirrors.sjtug.sjtu.edu.cn |                  |            Docker Hub、GCR 等             |
| [中科院软件所镜像站](https://mirror.iscas.ac.cn/mirror/docker.html) |        https://mirror.iscas.ac.cn        |                  |                Docker Hub                 |



## 检查加速是否生效
执行`docker info`,如果从结果中看到了如下内容，说明配置成功。
```bash
Registry Mirrors:
 [...]
 https://docker.m.daocloud.io
```

## docker 镜像测速
使用镜像前后，可使用 time 统计所花费的总时间。测速前先移除本地的镜像！
```sh
$ docker rmi node:latest
$ time docker pull node:latest
Pulling repository node
[...]

real   1m14.078s
user   0m0.176s
sys    0m0.120s
```

[参考](https://www.wxy97.com/archives/b5b225b6-7741-4560-be2f-2e6a4f671d9b)


