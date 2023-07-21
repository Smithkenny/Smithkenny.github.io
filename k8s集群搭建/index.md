# k8s集群搭建


# Kubernetes （1.22.2）安装手册（Ubuntu非高可用版）

## 安装前准备工作

### （重要！）安装k8s 1.22.2 版本需要的硬件条件：

所有节点：

**cpu数量2个及以上**

4G 以上内存

不用开虚拟化

**(重要！！)虚拟机不要用克隆，每台都要单独创建否则会出问题。**

未安装docker 之前创建一个快照。

安装docker、kubelet，并初始化集群后在创建一个快照。

未安装cni插件之前创建一个快照，以便于随时更改calico 和fluence

kubeadm 初始化失败检查，重置kubeadm。

ip 地址规划：

```Shell
master: 10.4.7.60/24 

gw:10.4.7.254

dns:114.114.114.114


worker1: 10.4.7.61/24

gw:10.4.7.254

dns:114.114.114.114


worker1: 10.4.7.61/24

gw:10.4.7.254

dns:114.114.114.114
```

**各节点安装时需要更换apt源：(更新和安装deb包都很快！！！)**

```Shell
http://mirrors.tuna.tsinghua.edu.cn/ubuntu/
```



### 1. 设置hosts解析

操作节点：所有节点（`master`）均需执行

- **修改hostname**
  hostname必须只能包含小写字母、数字、","、"-"，且开头结尾必须是小写字母或数字

``` Shell
# 在master节点
hostnamectl set-hostname master #设置master节点的hostname

# slave1节点
hostnamectl set-hostname worker1

# slave2节点
hostnamectl set-hostname worker2
```

### 2. 调整系统配置

操作节点： 所有的master和slave节点（`master,k8s-slave`）需要执行

>本章下述操作均以master为例，其他节点均是相同的操作（ip和hostname的值换成对应机器的真实值）

**设置iptables**

``` Shell
iptables -P FORWARD ACCEPT
/etc/init.d/ufw stop
ufw disable
```

- **关闭swap**

``` Shell
swapoff -a
# 防止开机自动挂载 swap 分区
rm /swap.img  
sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

- **修改内核参数**

``` Shell
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward=1
vm.max_map_count=262144
EOF
modprobe br_netfilter
sysctl -p /etc/sysctl.d/k8s.conf
```

- 设置apt源

```Shell
apt-get update && apt-get install -y apt-transport-https ca-certificates software-properties-common 
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main"

apt-get update   
#若上步出现NO_PUBLICKEY问题，参考https://www.cnblogs.com/jiangzuo/p/13667011.html
```

### 3. 安装docker

操作节点： 所有节点

``` python
apt-get install docker-ce=5:20.10.9~3-0~ubuntu-focal
systemctl enable docker && systemctl start docker
```

注意要关闭docker 默认的cgroup 否则会出现kubeadm 初始化失败！！！！

这是cgroup驱动问题。默认情况下Kubernetes cgroup驱动程序设置为system，但docker设置为systemd。我们需要更改Docker cgroup驱动，通过创建配置文件`/etc/docker/daemon.json`并添加以下行：

```javascript
{"exec-opts": ["native.cgroupdriver=systemd"]}
```

然后，为使配置生效，你必须重启docker和kubelet。

```Shell
systemctl daemon-reloadsystemctl restart dockersystemctl restart kubelet
```

重置kubeadm 

```Shell
sudo kubeadm reset
```

查看所有节点是否有污点

```Shell
kubectl get nodes -o json | jq '.items[].spec.taints'
```

### 给kubectl 设置别名为k，永久生效。

退出当前用户在进入就生效了。

```Shell
vi .bashrc
alias k='kubectl'
source ~/.bashrc
```

删除master节点上的污点，删除后pod可以调度到master上。

```Shell
kubectl taint nodes --all node-role.kubernetes.io/master-
```

### 使用kubectl中的强制删除命令

```Shell
kubectl delete pod podName -n NAMESPACE --force --grace-period=0
```

### 自动补全

```Shell
apt install bash-completion
// locate bash_completion
source /usr/share/bash-completion/bash_completion
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
source ~/.bashrc
```

查看所有pod 

```Shell
kubectl get po -owide  -A 
```



## 部署kubernetes

### 1. 安装 kubeadm, kubelet 和 kubectl

操作节点： 所有的master和slave节点(`master,k8s-slave`) 需要执行

``` Shell
apt-get install kubelet=1.22.2-00 kubectl=1.22.2-00 kubeadm=1.22.2-00
## 查看kubeadm 版本
kubeadm version
## 设置kubelet开机启动
systemctl enable kubelet 
```

### 2. 初始化配置文件

操作节点： 只在master节点（`master`）执行

``` Shell
kubeadm config print init-defaults > kubeadm.yaml

apiVersion: kubeadm.k8s.io/v1beta3
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfigurationx
localAPIEndpoint:
  advertiseAddress: 10.4.7.60 #修改为master 的IP
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: node     # 删掉此行，删掉此行，删掉此行
  imagePullPolicy: IfNotPresent
  taints: null
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta3
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns: {}
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
kubernetesVersion: 1.22.2
networking:
  dnsDomain: cluster.local
  podSubnet: 10.244.0.0/16   #添加此行
  serviceSubnet: 10.96.0.0/12
scheduler: {}
```


### 3. 提前下载镜像

操作节点：只在master节点（`master`）执行

```Shell
# 提前下载镜像到本地
kubeadm config images pull --config kubeadm.yaml

[config/images] Pulled registry.aliyuncs.com/google_containers/kube-apiserver:v1.22.2
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-controller-manager:v1.22.2
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-scheduler:v1.22.2
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-proxy:v1.22.2
[config/images] Pulled registry.aliyuncs.com/google_containers/pause:3.5
[config/images] Pulled registry.aliyuncs.com/google_containers/etcd:3.5.0-0
[config/images] Pulled registry.aliyuncs.com/google_containers/coredns:v1.8.4
```

### 4. 初始化master节点

操作节点：只在master节点（`k8s-master`）执行

``` Shell
kubeadm init --config kubeadm.yaml
```

若初始化成功后，最后会提示如下信息：

``` Shell
...
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.4.7.60:6443 --token abcdef.0123456789abcdef \
        --discovery-token-ca-cert-hash sha256:3a7987c9f5007ebac7980e6614281ee0e064c760c8db012471f9f662289cc9ce
```

接下来按照上述提示信息操作，配置kubectl客户端的认证

``` Shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

> **⚠️注意：**此时使用 kubectl get nodes查看节点应该处于notReady状态，因为还未配置网络插件
>
> 若执行初始化过程中出错，根据错误信息调整后，执行kubeadm reset后再次执行init操作即可

### 5. 添加slave节点到集群中

操作节点：所有的slave节点（`worker1`\`worker2`）需要执行
在每台slave节点，执行如下命令，该命令是在kubeadm init成功后提示信息中打印出来的，需要替换成实际init后打印出的命令。

```Shell
kubeadm join 10.4.7.60:6443 --token abcdef.0123456789abcdef \
        --discovery-token-ca-cert-hash sha256:3a7987c9f5007ebac7980e6614281ee0e064c760c8db012471f9f662289cc9ce
```

### 6. 安装calico插件

操作节点：只在master节点（`master`）执行

**方式一：直接安装**（未使用！！！）

- 下载资源文件

  ```Shell
  wget https://docs.projectcalico.org/manifests/calico-etcd.yaml
  ```

- 修改配置

  - 注释掉文件的前22行

    ```yaml
    1 ---
      2 # Source: calico/templates/calico-etcd-secrets.yaml
      3 # The following contains k8s Secrets for use with a TLS enabled etcd cluster.
      4 # For information on populating Secrets, see http://kubernetes.io/docs/user-guide/secrets/
      5 #apiVersion: v1
      6 #kind: Secret
      7 #type: Opaque
      8 #metadata:
      9 #  name: calico-etcd-secrets
     10 #  namespace: kube-system
     11 #data:
     12   # Populate the following with etcd TLS configuration if desired, but leave blank if
     13   # not using TLS for etcd.
     14   # The keys below should be uncommented and the values populated with the base64
     15   # encoded contents of each file that would be associated with the TLS data.
     16   # Example command for encoding a file contents: cat <file> | base64 -w 0
     17   # etcd-key: null
     18   # etcd-cert: null
     19   # etcd-ca: null
     20 ---
     21 # Source: calico/templates/calico-config.yaml
     22 # This ConfigMap is used to configure a self-hosted Calico installation.
     23 kind: ConfigMap
     24 apiVersion: v1
     25 metadata:
     26   name: calico-config
     27   namespace: kube-system
     ...
    ```

  - 修改configmap

    注意30-35行，其中etcd_endpoints换成环境的etcd地址

    ```yaml
     23 kind: ConfigMap
     24 apiVersion: v1
     25 metadata:
     26   name: calico-config
     27   namespace: kube-system
     28 data:
     29   # Configure this with the location of your etcd cluster.
     30   etcd_endpoints: "https://10.4.7.60:2379"
     31   # If you're using TLS enabled etcd uncomment the following.
     32   # You must also populate the Secret below with these files.
     33   etcd_ca: "/calico-secrets/etcd-ca"   # "/calico-secrets/etcd-ca"
     34   etcd_cert: "/calico-secrets/etcd-cert" # "/calico-secrets/etcd-cert"
     35   etcd_key: "/calico-secrets/etcd-key"  # "/calico-secrets/etcd-key"
     36   # Typha is disabled.
     37   typha_service_name: "none"
     38   # Configure the backend to use.
     39   calico_backend: "bird"
    ```

  - 添加calico-node环境变量

    注意297-302行为新添加

    ```yaml
    285       containers:
    286         # Runs calico-node container on each Kubernetes node. This
    287         # container programs network policy and routes on each
    288         # host.
    289         - name: calico-node
    290           image: docker.io/calico/node:v3.20.0
    291           envFrom:
    292           - configMapRef:
    293               # Allow KUBERNETES_SERVICE_HOST and KUBERNETES_SERVICE_PORT to be overridden for eBPF mode.
    294               name: kubernetes-services-endpoint
    295               optional: true
    296           env:
    297             - name: KUBERNETES_SERVICE_HOST
    298               value: "10.4.7.60"
    299             - name: KUBERNETES_SERVICE_PORT
    300               value: "6443"
    301             - name: KUBERNETES_SERVICE_PORT_HTTPS
    302               value: "6443"
    303             # The location of the etcd cluster.
    304             - name: ETCD_ENDPOINTS
    305               valueFrom:
    306                 configMapKeyRef:
    307                   name: calico-config
    308                   key: etcd_endpoints
    309             # Location of the CA certificate for etcd.
    310             - name: ETCD_CA_CERT_FILE
    ```

  - 修改CIDR

    注意371-372行，value值为k8s集群初始化的pod-network-cidr

    ```yaml
    370             # no effect. This should fall within `--cluster-cidr`.
    371             - name: CALICO_IPV4POOL_CIDR
    372               value: "10.244.0.0/16"
    373             # Disable file logging so `kubectl logs` works.
    374             - name: CALICO_DISABLE_FILE_LOGGING
    375               value: "true"
    ```

    创建secret

    ```Shell
    kubectl -n kube-system create secret generic calico-etcd-secrets --from-file=etcd-ca=/etc/kubernetes/pki/etcd/ca.crt --from-file=etcd-cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt --from-file=etcd-key=/etc/kubernetes/pki/etcd/healthcheck-client.key
    ```

    创建calico资源清单

    ```Shell
    kubectl apply -f calico-etcd.yaml
    ```

​               等待pod启动完成

```Shell
kubectl -n kube-system get po 

NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-59db5cf8fd-fpzdq   1/1     Running   1          32m
calico-node-d2xq4                          1/1     Running   1          32m
calico-node-ppzjk     
```

calico插件包：

```Shell
docker.io/calico/cni:v3.20.2

docker.io/calico/pod2daemon-flexvol:v3.20.2

docker.io/calico/node:v3.20.2
```



**方式二：使用operator安装**(已成功！！！！！！)

- 安装operator

  ```Shell
  kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
  ```

- 等待operator pod安装启动完成

  ```Shell
  kubectl -n tigera-operator get po
  
  NAME                               READY   STATUS    RESTARTS   AGE
  tigera-operator-698876cbb5-kfpb2   1/1     Running   0          38m
  ```

  > 镜像拉取比较慢，可以手动去节点docker pull拉取

- 编辑calico配置

  ```Shell
  vim custom-resources.yaml
  
  apiVersion: operator.tigera.io/v1
  kind: Installation
  metadata:
    name: default
  spec:
    # Configures Calico networking.
    calicoNetwork:
      # Note: The ipPools section cannot be modified post-install.
      ipPools:
      - blockSize: 26
        cidr: 10.244.0.0/16        #修改和pod cidr一致
        encapsulation: VXLANCrossSubnet
        natOutgoing: Enabled
        nodeSelector: all()
  
  ---
  
  # This section configures the Calico API server.
  # For more information, see: https://docs.projectcalico.org/v3.20/reference/installation/api#operator.tigera.io/v1.APIServer
  apiVersion: operator.tigera.io/v1
  kind: APIServer
  metadata:
    name: default
  spec: {}
  ```

- 创建calico配置

  ```Shell
  kubectl apply -f custom-resources.yaml
  ```

- 等待operator自动创建calico的pod

```Shell
# operator会自动创建calico-apiserver和calico-system两个命名空间以及必要的pod，等待pod启动完成即可
kubectl get ns

NAME               STATUS   AGE
calico-apiserver   Active   13m
calico-system      Active   19m

kubectl -n calico-apiserver get po

NAME                                READY   STATUS    RESTARTS   AGE
calico-apiserver-554fbf9554-b6kzv   1/1     Running   0          13m

kubectl -n calico-system get po

NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-868b656ff4-hn6qv   1/1     Running   0          20m
calico-node-qqrp9                          1/1     Running   0          20m
calico-node-r45z2                          1/1     Running   0          20m
calico-typha-5b64cf4b48-vws5j              1/1     Running   0          20m
calico-typha-5b64cf4b48-w6wqf              1/1     Running   0          20m
```

### 7. 验证集群

操作节点： 在master节点（`k8s-master`）执行

``` Shell
kubectl get nodes  #观察集群节点是否全部Ready
```

创建测试nginx服务

``` Shell
kubectl run  test-nginx --image=nginx:alpine
```

查看pod是否创建成功，并访问pod ip测试是否可用

``` Shell
kubectl get po -o wide

NAME                          READY   STATUS    RESTARTS   AGE   IP           NODE         NOMINATED NODE   READINESS GATES
test-nginx-5bd8859b98-5nnnw   1/1     Running   0          9s    10.244.1.2   k8s-slave1   <none>           <none>
curl 10.244.1.2

...
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

### 8. 清理环境

如果你的集群安装过程中遇到了其他问题，我们可以使用下面的命令来进行重置：

```Shell
# 在全部集群节点执行
kubeadm reset
ifconfig cni0 down && ip link delete cni0
ifconfig flannel.1 down && ip link delete flannel.1
rm -rf /run/flannel/subnet.env
rm -rf /var/lib/cni/
mv /etc/kubernetes/ /tmp
mv /var/lib/etcd /tmp
mv ~/.kube /tmp
iptables -F
iptables -t nat -F
ipvsadm -C
ip link del kube-ipvs0
ip link del dummy0
```



设置apt源报错。换源

```Shell
apt-get update && apt-get install -y apt-transport-https ca-certificates software-properties-common 
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main"

apt-get update   
#若上步出现NO_PUBLICKEY问题，参考https://www.cnblogs.com/jiangzuo/p/13667011.html
```

未安装cni插件pod状态

```Shell
 coredns-7f6cbbb7b8-c778v pod 不能启动因为未安装cni插件。 
  events:
  Warning  FailedScheduling  43s (x2 over 2m13s)  default-scheduler  0/3 nodes are available: 3 node(s) had taint {node.kubernetes.io/not-ready: }, that the pod didn't tolerate.
  
  root@master:~# k get po -A -owide 
NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE     IP          NODE      NOMINATED NODE   READINESS GATES
kube-system   coredns-7f6cbbb7b8-c778v         0/1     Pending   0          3m54s   <none>      <none>    <none>           <none>
kube-system   coredns-7f6cbbb7b8-px7cm         0/1     Pending   0          3m54s   <none>      <none>    <none>           <none>
kube-system   etcd-master                      1/1     Running   0          4m7s    10.4.7.70   master    <none>           <none>
kube-system   kube-apiserver-master            1/1     Running   0          4m7s    10.4.7.70   master    <none>           <none>
kube-system   kube-controller-manager-master   1/1     Running   0          4m7s    10.4.7.70   master    <none>           <none>
kube-system   kube-proxy-5w6c4                 1/1     Running   0          3m23s   10.4.7.71   worker1   <none>           <none>
kube-system   kube-proxy-6jnkr                 1/1     Running   0          3m20s   10.4.7.72   worker2   <none>           <none>
kube-system   kube-proxy-hcwtz                 1/1     Running   0          3m54s   10.4.7.70   master    <none>           <none>
kube-system   kube-scheduler-master            1/1     Running   0          4m8s    10.4.7.70   master    <none>           <none>
```




