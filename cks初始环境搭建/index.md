# CKS初始环境搭建


### 准备工作

| k8s主机名  |   IP地址   |  系统版本号  | k8s组件版本 |
| :--------: | :--------: | :----------: | :---------: |
| CKS-master | 10.4.7.180 | ubuntu 20.04 |   1.24.4    |
| CKS-node1  | 10.4.7.181 | ubuntu 20.04 |   1.24.4    |
| CKS-node2  | 10.4.7.182 | ubuntu 20.04 |   1.24.4    |

### 配置前准备

```sh
# 开root用户设置密码
sudo passwd root
# 切换root用户
su - root
# 更新
apt update 
# 安装ssh
apt install ssh -y 
# 允许root用户远程登录
PermitRootLogin yes
UseDNS no
# 重启
systemctl restart ssh
```

### 配置域名

```sh
# 需要对应的机器执行如下命令
hostnamectl set-hostname CKS-master
hostnamectl set-hostname CKS-node1
hostnamectl set-hostname CKS-node2
```

### 域名解析

```sh
echo -e "10.4.7.180 CKS-master\n10.4.7.181 CKS-node1\n10.4.7.182 CKS-node2" >> /etc/hosts

# 拷贝到远程集群机器
scp /etc/hosts root@10.4.7.181:/etc/
scp /etc/hosts root@10.4.7.182:/etc/
```

### 初始化部分

```sh
# 关闭防火墙
systemctl stop ufw.service && systemctl disable ufw.service
iptables -F 
iptables-save 
# 关闭swap
swapoff -a
sed -ri 's/.*swap.*/#&/' /etc/fstab
```

### vnc服务搭建（选做）

```sh
# 安装vnc远程服务
apt install xfce4 xfce4-goodies -y

# 选lightdm

apt install tightvncserver -y

# 设置远程密码
123qwe.

# 开启远程发现空白
vncserver

# 更改配置文件
mv .vnc/xstartup .vnc/xstartup.bak
nano .vnc/xstartup

#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &

# 给权限
chmod +x .vnc/xstartup
# 杀进程
vncserver -kill :2
# 再次启动远程
vncserver

# 测试：用vncview连接
ip:2
```

## 安装containerd

### 运行时Runc安装

```sh
# 下载二进制包 https://github.com/opencontainers/runc/releases/download/
wget https://github.com/opencontainers/runc/releases/download/v1.1.3/runc.amd64
# 执行Path和权限设置
mv runc.amd64 /usr/local/sbin/runc 
chmod +x /usr/local/sbin/runc
```

### 安装前准备

```shell
# 添加加载内核模块
cat << EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
# 加载内核模块
modprobe overlay
modprobe br_netfilter
#设置内核参数
cat << EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
# 应用内核参数
sysctl --system
```

### 开始安装

```shell
# 下载二进制包 https://github.com/containerd/containerd/releases/
wget https://github.com/containerd/containerd/releases/download/v1.6.8/containerd-1.6.8-linux-amd64.tar.gz
# 解压
tar -zxvf containerd-1.6.8-linux-amd64.tar.gz 
# 执行文件放置系统Path
mv ./bin/* /usr/local/bin/
# systemd 服务启动脚本文件配置
cat > /lib/systemd/system/containerd.service << EOF

# Copyright The containerd Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

[Unit]
Description=containerd container runtime
Documentation=https://containerd.io
After=network.target local-fs.target

[Service]
ExecStartPre=-/sbin/modprobe overlay
ExecStart=/usr/local/bin/containerd

Type=notify
Delegate=yes
KillMode=process
Restart=always
RestartSec=5
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNPROC=infinity
LimitCORE=infinity
LimitNOFILE=infinity
# Comment TasksMax if your systemd version does not supports it.
# Only systemd 226 and above support this version.
TasksMax=infinity
OOMScoreAdjust=-999

[Install]
WantedBy=multi-user.target

EOF
```

### 检测版本

```sh
runc -v
ctr -v
```

### 启动、关闭服务及查看服务状态

```sh
systemctl start containerd
systemctl restart containerd
systemctl status containerd
systemctl enable containerd
```

### 更换国内镜像仓库

```sh
mkdir -p /etc/containerd/
containerd config default | sudo tee /etc/containerd/config.toml
将
    sandbox_image = "k8s.gcr.io/pause:3.6"
改为
    sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.6"
将
    systemd_cgroup = false
改为
    systemd_cgroup = true

重启containerd
systemctl restart containerd
```

### 安装cni插件(选做)

```sh
wget https://github.com/containernetworking/plugins/releases/download/v1.1.1/cni-plugins-linux-amd64-v1.1.1.tgz
mkdir -p /opt/cni/bin
tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.1.1.tgz
```

## 安装三件套kubectl kubelet kubeadm

```sh
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
apt-get update
# 查看可用版本
apt-cache madison kubelet 
apt-get install -y kubelet kubeadm kubectl

systemctl enable kubelet

# tab补全
apt-get install bash-completion

source <(kubectl completion bash)
source <(kubeadm completion bash)
# 加到.bashrc 里
nano .bashrc
source <(kubeadm completion bash)
source <(kubectl completion bash)

# 给crictl添加tab补全
source <(crictl completion bash)
# 加到.bashrc 里
nano .bashrc
source <(crictl completion bash)
```

### 运行crictl images 时报错

```sh
WARN[0000] image connect using default endpoints: [unix:///var/run/dockershim.sock unix:///run/containerd/containerd.sock unix:///run/crio/crio.sock unix:///var/run/cri-dockerd.sock]. As the default settings are now deprecated, you should set the endpoint instead. 
ERRO[0000] unable to determine image API version: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing dial unix /var/run/dockershim.sock: connect: no such file or directory" 
E0820 17:31:44.920882    5408 remote_image.go:121] "ListImages with filter from image service failed" err="rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.ImageService" filter="&ImageFilter{Image:&ImageSpec{Image:,Annotations:map[string]string{},},}"
FATA[0000] listing images: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.ImageService 
```

### 排错

```sh
ll /run/containerd/
srw-rw----  1 root root   0 Aug 20 17:20 containerd.sock=

crictl config runtime-endpoint unix:///run/containerd/containerd.sock

# 更改配置
nano /etc/crictl.yaml

runtime-endpoint: "unix:///run/containerd/containerd.sock"
image-endpoint: "unix:///run/containerd/containerd.sock"
timeout: 10

# 重新加载
systemctl daemon-reload
# 发现还是报错
E0820 17:37:56.265740    5708 remote_image.go:121] "ListImages with filter from image service failed" err="rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.ImageService" filter="&ImageFilter{Image:&ImageSpec{Image:,Annotations:map[string]string{},},}"
FATA[0000] listing images: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.ImageService 

# 这是因为还需要手动设置runtime_type
nano /etc/containerd/config.toml
[plugins."io.containerd.grpc.v1.cri".containerd.default_runtime] 下的
        runtime_type = ""
改成
        runtime_type = "io.containerd.runtime.v1.linux"

# 重启containerd 
systemctl daemon-reload
systemctl restart containerd

# 正常了
root@cks-master:~# crictl images
IMAGE               TAG                 IMAGE ID            SIZE

```

### 集群初始化（cks-master上操作）

`kubeadm config print init-defaults > init.yaml
nano init.yaml `

```sh
  advertiseAddress: 10.4.7.180
  name: cks-master
imageRepository: registry.aliyuncs.com/google_containers
kubernetesVersion: 1.24.4
```

`kubeadm init --config=init.yaml`

#### 各个节点加入集群

```sh
kubeadm join 10.4.7.180:6443 --token abcdef.0123456789abcdef \
	--discovery-token-ca-cert-hash sha256:96f4398d1e19fbaed92c0401a2d38031f89470dc1faf84669306c6d85ba8cc45
```

master 节点上输入以下命令，用kubectl 控制集群。

```shell
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### 安装cni插件（calico）

```sh
https://projectcalico.docs.tigera.io/getting-started/kubernetes/self-managed-onprem/onpremises
```

```sh
# 小于50个node用这个连接
curl https://raw.githubusercontent.com/projectcalico/calico/v3.24.0/manifests/calico.yaml -O
```

#### 更改calico.yaml 文件

nano 用`ctrl+ w`搜索。

```sh
            - name: CALICO_IPV4POOL_CIDR
              value: "10.96.0.0/12"
```

开始创建

```sh
kubectl apply -f calico.yaml
```

等待慢慢创建pod

```sh
kubectl get po -A 
```

都running后集群搭建成功

```sh
root@cks-master:~# kubectl get po -A -w
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-5b97f5d8cf-glx5x   1/1     Running   0          15m
kube-system   calico-node-87vt9                          1/1     Running   0          15m
kube-system   calico-node-dd8nk                          1/1     Running   0          15m
kube-system   calico-node-jgmfk                          1/1     Running   0          15m
kube-system   coredns-74586cf9b6-46xtt                   1/1     Running   0          27m
kube-system   coredns-74586cf9b6-7jkkd                   1/1     Running   0          27m
kube-system   etcd-cks-master                            1/1     Running   0          27m
kube-system   kube-apiserver-cks-master                  1/1     Running   0          27m
kube-system   kube-controller-manager-cks-master         1/1     Running   0          27m
kube-system   kube-proxy-dhgbc                           1/1     Running   0          24m
kube-system   kube-proxy-pvkgx                           1/1     Running   0          27m
kube-system   kube-proxy-zckg5                           1/1     Running   0          24m
kube-system   kube-scheduler-cks-master                  1/1     Running   0          27m
```

由于`coredns`都在被分配在同一个节点上，没冗余，需要重新分配在不同节点上。

```sh
root@cks-master:~# kubectl get po -A -owide | grep coredns
kube-system   coredns-74586cf9b6-46xtt                   1/1     Running   0          30m   10.106.109.130   cks-node2    <none>           <none>
kube-system   coredns-74586cf9b6-7jkkd                   1/1     Running   0          30m   10.106.109.131   cks-node2    <none>           <none>
```

#### 重新分配coredns

```sh
root@cks-master:~# kubectl -n kube-system rollout restart deployment coredns 
root@cks-master:~# kubectl get po -A -owide -w | grep coredns 
kube-system   coredns-75ffbc4979-gjxjn                   1/1     Running   0          2m37s   10.106.109.132   cks-node2    <none>           <none>
kube-system   coredns-75ffbc4979-pbfmt                   1/1     Running   0          2m37s   10.104.127.129   cks-node1    <none>           <none>
```

#### 延长证书有效期

证书默认1年后就会过期。可以通过如下方式修改为10年。

```sh
$ cd /etc/kubernetes/pki

# 查看当前证书有效期
$ for i in $(ls *.crt); do echo "===== $i ====="; openssl x509 -in $i -text -noout | grep -A 3 'Validity' ; done

$ mkdir backup_key; cp -rp ./* backup_key/
$ git clone https://github.com/yuyicai/update-kube-cert.git
$ cd update-kube-cert/ 
$ bash update-kubeadm-cert.sh all

# 重建管理服务
$ kubectl -n kube-system delete po kube-apiserver-k8s-master kube-controller-manager-k8s-master kube-scheduler-k8s-master
```

### 部署dashboard（k8s 可视化页面）

github上搜索dashboard

```sh
https://github.com/kubernetes/dashboard/blob/master/docs/user/installation.md
```

下载默认配置文件

```sh
https://raw.githubusercontent.com/kubernetes/dashboard/v2.6.1/aio/deploy/recommended.yaml
```

不用更改配置直接部署,等待部署完成。

```sh
kubectl apply -f recommended.yaml
```

默认部署后dashboard是ClusterIP 类型，只允许集群内部访问。

```sh
root@cks-master:~# kubectl -n kubernetes-dashboard get svc 
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.96.205.151   <none>        8000/TCP   14m
kubernetes-dashboard        ClusterIP   10.110.3.32     <none>        443/TCP    14m
```

更改类型为NodePort，暴露端口，可以在集群外部访问。

```sh
root@cks-master:~# kubectl -n kubernetes-dashboard edit svc kubernetes-dashboard

  ports:
  - port: 443
    protocol: TCP
    targetPort: 8443
    nodePort: 30003
  selector:
    k8s-app: kubernetes-dashboard
  sessionAffinity: None
  type: Nodeport

```

保存退出会自动重启pod

此时可以用ip+端口号形式访问dashboard.

```sh
root@cks-master:~# kubectl -n kubernetes-dashboard get svc -w 
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
dashboard-metrics-scraper   ClusterIP   10.96.205.151   <none>        8000/TCP        46m
kubernetes-dashboard        NodePort    10.110.3.32     <none>        443:30003/TCP   46m
```

```sh
https://10.4.7.180:30003
```

然后使用token来登录



#### 登录前根据官方文档要先创建用户

官网说明

```sh
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
```

```sh
admin.yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard

```

创建用户

```sh
kubectl apply -f admin.yaml
```

查看

```sh
root@cks-master:~# kubectl -n kubernetes-dashboard get sa
NAME                   SECRETS   AGE
admin-user             0         66s
default                0         56m
kubernetes-dashboard   0         56m

# 1.24版本已经默认不支持自动创建token，需要手动创建
root@cks-master:~# kubectl -n kubernetes-dashboard describe sa admin-user 
Name:                admin-user
Namespace:           kubernetes-dashboard
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   <none>
Tokens:              <none>
Events:              <none>
```

手动创建token

```sh
root@cks-master:~# kubectl -n kubernetes-dashboard create token admin-user 
eyJhbGciOiJSUzI1NiIsImtpZCI6ImVlLTUwa0JWcm5seXlfemJ2Ui15MnpuTXJQVXctLXVPWFBsc3IzQl92dTAifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNjYxMDA4NzkzLCJpYXQiOjE2NjEwMDUxOTMsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiNmE3YWU1ODAtNDhiNi00NGVmLWFmNzUtNjY2MTQzOGUzMTcyIn19LCJuYmYiOjE2NjEwMDUxOTMsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.kvcfZmYX_CbX6_qaV1GQrT6Z7FCjGwOvG40OZ1cMEUhNQdV9G5XuCjSMJ4FrnKsiNOcSLv6_wdwWMZpWIgvbWYbBJQQ7Q-0Djie_iPzbGbJKbEvEsaNMLqIb7liiM52nKl0TJJweQpIoUHHWf0oRiGDjSkms5QVqDmlddnr5i8z2NotwW7FyIfml1E_OZFCQ-vHnVlo8ONGflkJyAutqGxRghBOBMuInMKSXfpk3AaXf6cjtButHRVqp_jDgErVVkKqtRJrqSHoDwMBV7NRn__fqdrfCGiEVKYlucMdv78TgKPsK2313OE3Arbrf0q_prP3Cs6VAILHETGdmhNHFlA
```

使用token登录dashboard

每次登录都需要重新创建token



k8s 不能干什么？

修改内核的应用

不建议部署数据库,很容易丢数据。




