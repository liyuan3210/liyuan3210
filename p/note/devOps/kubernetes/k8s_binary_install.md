# kubernetes二进制安装

### 一．创建多台虚拟机，安装linux操作系统

docker版本：v19.03.9

kubernetes版本：v1.19.7

etcd版本：v3.4.14

### 二．操作系统初始化

见kubeadm安装一样

### 三．为etcd和apiserver自签证书

官方也可以申请，一般用自签证书即可(自己做的)，自签证书分两种：

内部访问证书，外部访问证书

https://www.jianshu.com/p/944f2003c829

https://blog.51cto.com/liuzhengwei521/2120535?utm_source=oschina-app

常用证书类型：cfssl与openssl



安装cfssl工具：

```
1、安装cfssl工具(脚本)
另存为cfsslInstall.sh
wget https://pkg.cfssl.org/R1.2/cfssl_linux-amd64
wget https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
wget https://pkg.cfssl.org/R1.2/cfssl-certinfo_linux-amd64
chmod +x cfssl_linux-amd64 cfssljson_linux-amd64 cfssl-certinfo_linux-amd64
mv cfssl_linux-amd64 /usr/local/bin/cfssl
mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
mv cfssl-certinfo_linux-amd64 /usr/bin/cfssl-certinfo
```

1.etcd证书，创建一个etcd目录

```
1>创建ca-config.json文件/自签证书颁发机构（CA）
cat > ca-config.json<< EOF
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "www": {
        "expiry": "87600h",
        "usages": [
          "signing",
          "key encipherment",
          "server auth",
          "client auth"
        ]
      }
    }
  }
}
EOF

2>创建ca-csr.json文件
cat > ca-csr.json<< EOF
{
  "CN": "etcd CA",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShangHai",
      "ST": "ShangHai"
    }
  ]
}
EOF

3>生成证书
$ cfssl gencert -initca ca-csr.json | cfssljson -bare ca -
$ ls *pem 	//查看生成的证书

4>使用自签 CA 签发 Etcd HTTPS 证书
cat > server-csr.json<< EOF
{
  "CN": "etcd",
  "hosts": [
    "192.168.122.242",
    "192.168.122.84",
    "192.168.122.177"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShangHai",
      "ST": "ShangHai"
    }
  ]
}
EOF
#注：上述文件 hosts 字段中 IP 为所有 etcd 节点的集群内部通信 IP，一个都不能少！为了方便后期扩容可以多写几个预留的 IP。

生成签发证书:
$ cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=www server-csr.json | cfssljson -bare server
$ ls server*pem		//查看
```

2.apiServer证书，创建一个k8s目录

自签证书怎么做(方法)：
		1.添加可信任ip列表(常用方式)
		2.携带ca证书发送

```
1>自签证书颁发机构（CA）
cat > ca-config.json<< EOF
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "kubernetes": {
        "expiry": "87600h",
        "usages": [
          "signing",
          "key encipherment",
          "server auth",
          "client auth"
        ]
      }
    }
  }
}
EOF

2>创建ca-csr.json文件
cat > ca-csr.json<< EOF
{
  "CN": "kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "Beijing",
      "ST": "Beijing",
      "O": "k8s",
      "OU": "System"
    }
  ]
}
EOF

3>生成证书
cfssl gencert -initca ca-csr.json | cfssljson -bare ca -
ls *pem

4>使用自签 CA 签发 kube-apiserver HTTPS 证书
cat > server-csr.json<< EOF
{
  "CN": "kubernetes",
  "hosts": [
    "10.0.0.1",
    "127.0.0.1",
    "192.168.122.242",
    "192.168.122.84",
    "192.168.122.177",
    "kubernetes",
    "kubernetes.default",
    "kubernetes.default.svc",
    "kubernetes.default.svc.cluster",
    "kubernetes.default.svc.cluster.local"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "BeiJing",
      "ST": "BeiJing",
      "O": "k8s",
      "OU": "System"
    }
  ]
}
EOF
//上面添加可信任的ip列表
生成签发证书:
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes server-csr.json | cfssljson -bare server
ls server*pem	//查看
```

3.kube-proxy证书(生成 kube-proxy.kubeconfig 文件)

```
cat > kube-proxy-csr.json<< EOF
{
	"CN": "system:kube-proxy",
	"hosts": [],
	"key": {
		"algo": "rsa",
		"size": 2048
	},
	"names": [{
		"C": "CN",
		"L": "ShangHai",
		"ST": "ShangHai",
		"O": "k8s",
		"OU": "System"
	}]
}
EOF

# 生成证书
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -
profile=kubernetes kube-proxy-csr.json | cfssljson -bare kube-proxy

# 查看
ls kube-proxy*pem
kube-proxy-key.pem kube-proxy.pem

```



### 四．部署etcd集群

下载并解压etcd:

wget https://github.com/etcd-io/etcd/releases/download/v3.4.9/etcd-v3.4.9-linux-amd64.tar.gz

tar xzvf etcd-v3.4.9-linux-amd64.tar.gz  //解压后安装只需etcd	， etcdctl两个执行文件即可

创建etcd自定义安装目录：

mkdir etcd&&mkdir etcd/bin etcd/cfg etcd/ssl

1>拷贝etcd,etcdctl两执行文件到etcd/bin目录下面

2>拷贝生成的证书文件ca.pem，server-key.pem，server.pem到etcd/ssl下面	//生成方法见第`三章`etcd证书生成

3>创建配置etcd.conf文件到etcd/cfg目录下面

```
cat > etcd.conf << EOF
#[Member]
ETCD_NAME="etcd-1"
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="https://192.168.31.71:2380"
ETCD_LISTEN_CLIENT_URLS="https://192.168.31.71:2379"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.31.71:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.31.71:2379"
ETCD_INITIAL_CLUSTER="etcd-1=https://192.168.31.71:2380,etcd2=https://192.168.31.72:2380,etcd-3=https://192.168.31.73:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF
```

**每个节点要修改的字段(要修改的)：**

```
ETCD_NAME							//节点名称，不能重复
ETCD_LISTEN_PEER_URLS				//当前节点ip
ETCD_LISTEN_CLIENT_URLS				//当前节点ip
ETCD_INITIAL_ADVERTISE_PEER_URLS	//当前节点ip
ETCD_ADVERTISE_CLIENT_URLS			//当前节点ip
ETCD_INITIAL_CLUSTER				//集群节点数组
```

描述：

```
ETCD_NAME：节点名称，集群中唯一
ETCD_DATA_DIR：数据目录
ETCD_LISTEN_PEER_URLS：集群通信监听地址
ETCD_LISTEN_CLIENT_URLS：客户端访问监听地址
ETCD_INITIAL_ADVERTISE_PEER_URLS：集群通告地址
ETCD_ADVERTISE_CLIENT_URLS：客户端通告地址
ETCD_INITIAL_CLUSTER：集群节点地址
ETCD_INITIAL_CLUSTER_TOKEN：集群 Token
ETCD_INITIAL_CLUSTER_STATE：加入集群的当前状态，new 是新集群，existing 表示加入
已有集群
```

4>创建etcd服务文件

```
cat > /usr/lib/systemd/system/etcd.service << EOF
[Unit]
Description=Etcd Server
After=network.target
After=network-online.target
Wants=network-online.target
[Service]
Type=notify
EnvironmentFile=/opt/etcd/cfg/etcd.conf
ExecStart=/opt/etcd/bin/etcd \
--cert-file=/opt/etcd/ssl/server.pem \
--key-file=/opt/etcd/ssl/server-key.pem \
--peer-cert-file=/opt/etcd/ssl/server.pem \
--peer-key-file=/opt/etcd/ssl/server-key.pem \
--trusted-ca-file=/opt/etcd/ssl/ca.pem \
--peer-trusted-ca-file=/opt/etcd/ssl/ca.pem \
--logger=zap
Restart=on-failure
LimitNOFILE=65536
[Install]
WantedBy=multi-user.target
EOF
```

使用scp发送/usr/lib/systemd/system/etcd.service至远程节点

5>使用scp发送制作好的安装包etcd到远程节点/opt/

```
$ scp -r etcd root@node1:/opt/
```

6>启动服务并设置自启动

```
systemctl daemon-reload
systemctl start etcd
systemctl enable etcd
```

7>查看集群状态

```
如果输出上面信息，就说明集群部署成功。如果有问题第一步先看日志：
/var/log/message 或 journalctl -u etcd
```



### 五．部署master组件

下载kubernetes:v1.19.7，下载server端就可以了

$ wget https://dl.k8s.io/v1.19.7/kubernetes-server-linux-amd64.tar.gz

创建k8s自定义安装目录：

mkdir kubernetes&&mkdir kubernetes/bin kubernetes/cfg kubernetes/ssl kubernetes/logs

1>拷贝kube-apiserver，kube-controller-manager，kubectl，kube-scheduler至k8s/bin目录

2>拷贝前面生成的apiServer证书ca-key.pem，ca.pem，server-key.pem，server.pem到k8s/ssl里面

3>创建配置文件kube-apiserver.conf

```
cat > kube-apiserver.conf << EOF
KUBE_APISERVER_OPTS="--logtostderr=false --v=2 \\
--log-dir=/opt/kubernetes/logs \\
--etcdservers=https://192.168.31.71:2379,https://192.168.31.72:2379,https://192.168.31.73:2379 \\
--bind-address=192.168.31.71 \\
--secure-port=6443 \\
--advertise-address=192.168.31.71 \\
--allow-privileged=true \\
--service-cluster-ip-range=10.0.0.0/24 \\
--enable-admissionplugins=NamespaceLifecycle,LimitRanger,ServiceAccount,ResourceQuota,NodeRestriction \\
--authorization-mode=RBAC,Node \\
--enable-bootstrap-token-auth=true \\
--token-auth-file=/opt/kubernetes/cfg/token.csv \\
--service-node-port-range=30000-32767 \\
--kubelet-client-certificate=/opt/kubernetes/ssl/server.pem \\
--kubelet-client-key=/opt/kubernetes/ssl/server-key.pem \\
--tls-cert-file=/opt/kubernetes/ssl/server.pem \\
--tls-private-key-file=/opt/kubernetes/ssl/server-key.pem \\
--client-ca-file=/opt/kubernetes/ssl/ca.pem \\
--service-account-key-file=/opt/kubernetes/ssl/ca-key.pem \\
--etcd-cafile=/opt/etcd/ssl/ca.pem \\
--etcd-certfile=/opt/etcd/ssl/server.pem \\
--etcd-keyfile=/opt/etcd/ssl/server-key.pem \\
--audit-log-maxage=30 \\
--audit-log-maxbackup=3 \\
--audit-log-maxsize=100 \\
--audit-log-path=/opt/kubernetes/logs/k8s-audit.log"
EOF
```

**需要改的字段：**

```
etcdservers	//etcd集群ip
bind-address //主节点Ip
advertise-address //主节点Ip
```

描述：

```
–logtostderr：启用日志
—v：日志等级
–log-dir：日志目录
–etcd-servers：etcd 集群地址
–bind-address：监听地址
–secure-port：https 安全端口
–advertise-address：集群通告地址
–allow-privileged：启用授权
–service-cluster-ip-range：Service 虚拟 IP 地址段
–enable-admission-plugins：准入控制模块
–authorization-mode：认证授权，启用 RBAC 授权和节点自管理
–enable-bootstrap-token-auth：启用 TLS bootstrap 机制
–token-auth-file：bootstrap token 文件
–service-node-port-range：Service nodeport 类型默认分配端口范围
–kubelet-client-xxx：apiserver 访问 kubelet 客户端证书
–tls-xxx-file：apiserver https 证书
–etcd-xxxfile：连接 Etcd 集群证书
–audit-log-xxx：审计日志
```

4>创建配置文件kube-controller-manager.conf，kube-scheduler.conf

```
cat > kube-controller-manager.conf << EOF
KUBE_CONTROLLER_MANAGER_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--leader-elect=true \\
--master=127.0.0.1:8080 \\
--bind-address=127.0.0.1 \\
--allocate-node-cidrs=true \\
--cluster-cidr=10.244.0.0/16 \\
--service-cluster-ip-range=10.0.0.0/24 \\
--cluster-signing-cert-file=/opt/kubernetes/ssl/ca.pem \\
--cluster-signing-key-file=/opt/kubernetes/ssl/ca-key.pem \\
--root-ca-file=/opt/kubernetes/ssl/ca.pem \\
--service-account-private-key-file=/opt/kubernetes/ssl/ca-key.pem \\
--experimental-cluster-signing-duration=87600h0m0s"
EOF

描述：
–master：通过本地非安全本地端口 8080 连接 apiserver。
–leader-elect：当该组件启动多个时，自动选举（HA）
–cluster-signing-cert-file/–cluster-signing-key-file：自动为 kubelet 颁发证书
的 CA，与 apiserver 保持一致
```

配置文件kube-scheduler.conf

```
cat > kube-scheduler.conf << EOF
KUBE_SCHEDULER_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--leader-elect \\
--master=127.0.0.1:8080 \\
--bind-address=127.0.0.1"
EOF

描述
–master：通过本地非安全本地端口 8080 连接 apiserver。
–leader-elect：当该组件启动多个时，自动选举（HA）
```

5>创建服务启动文件

```
创建kube-apiserver.service文件
cat > kube-apiserver.service << EOF
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-apiserver.conf
ExecStart=/opt/kubernetes/bin/kube-apiserver \$KUBE_APISERVER_OPTS
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF

创建kube-controller-manager.service文件
cat > kube-controller-manager.service << EOF
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-controller-manager.conf
ExecStart=/opt/kubernetes/bin/kube-controller-manager \$KUBE_CONTROLLER_MANAGER_OPTS
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF

创建kube-scheduler.service文件
cat > kube-scheduler.service << EOF
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-scheduler.conf
ExecStart=/opt/kubernetes/bin/kube-scheduler \$KUBE_SCHEDULER_OPTS
Restart=on-failure
[Install]
WantedBy=multi-user.target
EOF
```
启动并设置开机启动（上面三个服务都做）：
```
systemctl daemon-reload
systemctl start kube-controller-manager
systemctl enable kube-controller-manager
```

6>验证（查看健康状态）

```
$ kubectl get cs
```

7>授权 kubelet-bootstrap 用户允许请求证书

```
$ kubectl create clusterrolebinding kubelet-bootstrap \
--clusterrole=system:node-bootstrapper \
--user=kubelet-bootstrap
```



### 六．部署node组件(每个node节点都要安装)

**1.安装docker**

下载并解压docker:

wget https://download.docker.com/linux/static/stable/x86_64/docker-19.03.9.tgz

```
$ tar tar zxvf docker-19.03.9.tgz

$ mv docker/* /usr/bin
```

服务安装：

```
cat > docker.service << EOF
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
[Service]
Type=notify
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
[Install]
WantedBy=multi-user.target
EOF
```

```
mkdir /etc/docker
cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://6brt8p5b.mirror.aliyuncs.com"]
}
EOF
```

```
systemctl daemon-reload
systemctl start docker
systemctl enable docker
```

**2.安装kubelet，kube-proxy**

2.1>安装：

```
在所有 worker node 创建工作目录：
$ mkdir -p /opt/kubernetes/{bin,cfg,ssl,logs}
从master节点拷贝
scp kubelet kube-proxy node:/opt/kubernetes/bin
```

2.2>创建配置文件
修改bootstrap.kubeconfig文件

```
#生成 bootstrap.kubeconfig 文件
KUBE_APISERVER="https://192.168.31.71:6443" # apiserver IP:PORT
TOKEN="c47ffb939f5ca36231d9e3121a252940" # 与 token.csv 里保持一致
# 生成 kubelet bootstrap kubeconfig 配置文件
kubectl config set-cluster kubernetes \
--certificate-authority=/opt/kubernetes/ssl/ca.pem \
--embed-certs=true \
--server=${KUBE_APISERVER} \
--kubeconfig=bootstrap.kubeconfig
kubectl config set-credentials "kubelet-bootstrap" \
--token=${TOKEN} \
--kubeconfig=bootstrap.kubeconfig
kubectl config set-context default \
--cluster=kubernetes \
--user="kubelet-bootstrap" \
--kubeconfig=bootstrap.kubeconfig
kubectl config use-context default --kubeconfig=bootstrap.kubeconfig

拷贝到配置文件路径：
cp bootstrap.kubeconfig /opt/kubernetes/cfg
```
修改kube-proxy.kubeconfig
```
生成 kubeconfig 文件：
KUBE_APISERVER="https://192.168.31.71:6443"
kubectl config set-cluster kubernetes \
--certificate-authority=/opt/kubernetes/ssl/ca.pem \
--embed-certs=true \
--server=${KUBE_APISERVER} \
--kubeconfig=kube-proxy.kubeconfig
kubectl config set-credentials kube-proxy \
--client-certificate=./kube-proxy.pem \
--client-key=./kube-proxy-key.pem \
--embed-certs=true \
--kubeconfig=kube-proxy.kubeconfig
kubectl config set-context default \
--cluster=kubernetes \
--user=kube-proxy \
--kubeconfig=kube-proxy.kubeconfig
kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig

拷贝到配置文件指定路径：
cp kube-proxy.kubeconfig /opt/kubernetes/cfg/
```
修改kubelet.conf
```
cat > /opt/kubernetes/cfg/kubelet.conf << EOF
KUBELET_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--hostname-override=k8s-master \\
--network-plugin=cni \\
--kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig \\
--bootstrap-kubeconfig=/opt/kubernetes/cfg/bootstrap.kubeconfig \\
--config=/opt/kubernetes/cfg/kubelet-config.yml \\
--cert-dir=/opt/kubernetes/ssl \\
--pod-infra-container-image=lizhenliang/pause-amd64:3.0"
EOF
描述：
–hostname-override：显示名称，集群中唯一
–network-plugin：启用 CNI –kubeconfig：空路径，会自动生成，后面用于连接 apiserver –bootstrap-kubeconfig：首次启动向 apiserver 申请证书
–config：配置参数文件
–cert-dir：kubelet 证书生成目录
–pod-infra-container-image：管理 Pod 网络容器的镜像
```
修改kubelet-config.yml
```
cat > /opt/kubernetes/cfg/kubelet-config.yml << EOF
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
address: 0.0.0.0
port: 10250
readOnlyPort: 10255
cgroupDriver: cgroupfs
clusterDNS:
- 10.0.0.2
clusterDomain: cluster.local
failSwapOn: false
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 2m0s
    enabled: true
  x509:
    clientCAFile: /opt/kubernetes/ssl/ca.pem
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 5m0s
    cacheUnauthorizedTTL: 30s
evictionHard:
imagefs.available: 15%
memory.available: 100Mi
nodefs.available: 10%
nodefs.inodesFree: 5%
maxOpenFiles: 1000000
maxPods: 110
EOF
```

kube-proxy.conf
```
cat > /opt/kubernetes/cfg/kube-proxy.conf << EOF
KUBE_PROXY_OPTS="--logtostderr=false \\
--v=2 \\
--log-dir=/opt/kubernetes/logs \\
--config=/opt/kubernetes/cfg/kube-proxy-config.yml"
EOF
```
kube-proxy-config.yaml
```
cat > /opt/kubernetes/cfg/kube-proxy-config.yml << EOF
kind: KubeProxyConfiguration
apiVersion: kubeproxy.config.k8s.io/v1alpha1
bindAddress: 0.0.0.0
metricsBindAddress: 0.0.0.0:10249
clientConnection:
  kubeconfig: /opt/kubernetes/cfg/kube-proxy.kubeconfig
hostnameOverride: k8s-master
clusterCIDR: 10.0.0.0/24
EOF
```

2.3>创建启动文件

```
cat > /usr/lib/systemd/system/kubelet.service << EOF
[Unit]
Description=Kubernetes Kubelet
After=docker.service
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kubelet.conf
ExecStart=/opt/kubernetes/bin/kubelet \$KUBELET_OPTS
Restart=on-failure
LimitNOFILE=65536
[Install]
WantedBy=multi-user.target
EOF
```

```
cat > /usr/lib/systemd/system/kube-proxy.service << EOF
[Unit]
Description=Kubernetes Proxy
After=network.target
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-proxy.conf
ExecStart=/opt/kubernetes/bin/kube-proxy \$KUBE_PROXY_OPTS
Restart=on-failure
LimitNOFILE=65536
[Install]
WantedBy=multi-user.target
EOF
```

启动并设置开机启动（上面两个服务都做）

```
systemctl daemon-reload
systemctl start kube-proxy
systemctl enable kube-proxy
```

3.部署 CNI 网络

### 七．部署集群网络



### kubernetes手动安装etcd,flannel

```
官网:https://kubernetes.io
中文网:https://www.kubernetes.org.cn

下载：
	首页进入>Documentation
				>Download Current Release
					>Client Binaries 	(include windows)
					>Server Binaries
					>Node Binaries		(include windows)
							
	服务端:https://dl.k8s.io/v1.10.0/kubernetes-server-linux-arm64.tar.gz
	客户端:https://dl.k8s.io/v1.10.0/kubernetes-client-linux-amd64.tar.gz
	节点端:https://dl.k8s.io/v1.10.0/kubernetes-node-linux-arm64.tar.gz

kubernetes安装版本
	minikub安装
	https://kubernetes.io/docs/getting-started-guides/minikube/

	kubernetes集群安装
	https://kubernetes.io/docs/setup/independent/install-kubeadm/

	IBM Cloud Private Ce版
	https://hub.docker.com/r/ibmcom/cfc-installer/

kubernetes视频地址
	https://www.kubernetes.org.cn/3546.html
	
	
手动集群安装
https://www.kubernetes.org.cn/3096.html
https://www.cnblogs.com/sammyliu/p/8182078.html
	

1.etcd(数据库)
2.journeys(方案程序)

安装配置
https://www.hi-linux.com/posts/30481.html
https://blog.csdn.net/zzq900503/article/details/81710319

----------------------------------------------------------------------------------------------------
一.理论部分

视频教程
https://www.bilibili.com/video/av63952062/?p=11
docker compose(单机编排),docker swarm(多台机器编排),docker machine(怎么安装成docker主机)
mesos marathon
kubernetes(深受google容器编排系统borg的影响)

kubernetes组成部分
master/node
	master ：API server , Scheduler , Controller Manager ,ectd
	node ：kubelet,docker...(还有其它第三方容器)
Pod(kubernetes最小组成部分)
	一般一个pod管理一个docker,(一个pod管理多个docker,一个为主其它为辅)
	lable ,lable selector
	
pod管理方式(特定应用管理)
	1.自主式pod(node挂掉后就不存在了)
	2.控制器管理的pod
		ReplicationController(早期的副本控制器,nginx副本不够了再启动一个,多退少补)
		支持更新nginx版本,滚动更新
		ReplicaSet(新版本,不直接管理通过Deployment)
		Deployment(用的最多的,声明管理无状态的,支持二级控制器HPA)
		StatefulSet(有状态管)
		daemonSet(在每个node上运行)
		Job,Ctonjon(运行作业,周期)
HPA(实际按需增加容器数量)
	HorizontalPodAutoscaler
	
服务发现(service提供一个稳定的访问地址)
	当一个Pod挂掉,pod会重新启动一个,对应的ip也会改变,这样需要服务发现来解决问题


kubernetes网络模型(三种网络)
节点网络(master,node1,node2)
↓
pod网络(pod网络)
↓
service网络(固定不变的地址)

叠加网络Overlay Network
	同一个pod内的多个容器间通信:lo
	pod之间的通信(pod网络)
	pod与service通信,kube-proxy(server网络)
	
	
CNI：遵循CNI(namespace是kubernetes网络管理边界)
	flannel：网络配置(叠加网络)
	calico：网络配置,网络策略(每个pod之间的隔离)
	canel：网络策略
	...
----------------------------------------------------------------------------------------------------
etcd官网:https://coreos.com/etcd/
项目地址：https://github.com/coreos/etcd

安装配置
https://blog.csdn.net/god_wot/article/details/77854093
https://blog.csdn.net/yunlilang/article/details/79725400

单机启动
etcd --name myetcd1  --listen-client-urls http://0.0.0.0:2379 --advertise-client-urls http://0.0.0.0:2379 --listen-peer-urls http://0.0.0.0:2380 --initial-advertise-peer-urls http://0.0.0.0:2380  --initial-cluster myetcd1=http://0.0.0.0:2380

etcd 
--name myetcd1  
--listen-client-urls http://0.0.0.0:2379 
--advertise-client-urls http://0.0.0.0:2379 
--listen-peer-urls http://0.0.0.0:2380 
--initial-advertise-peer-urls http://0.0.0.0:2380  
--initial-cluster myetcd1=http://0.0.0.0:2380

集群启动
etcd --name myetcd1  --listen-client-urls http://0.0.0.0:2379 --advertise-client-urls http://192.168.9.100:2379 --listen-peer-urls http://0.0.0.0:2380 --initial-advertise-peer-urls http://192.168.9.100:2380  --initial-cluster-token etcd-cluster-test --initial-cluster-state new --initial-cluster myetcd1=http://192.168.9.100:2380,myetcd2=http://192.168.9.100:2381,myetcd3=http://192.168.9.100:2382

etcd --name myetcd2  --listen-client-urls http://0.0.0.0:2389 --advertise-client-urls http://192.168.9.100:2389 --listen-peer-urls http://0.0.0.0:2381 --initial-advertise-peer-urls http://192.168.9.100:2381  --initial-cluster-token etcd-cluster-test --initial-cluster-state new --initial-cluster myetcd1=http://192.168.9.100:2380,myetcd2=http://192.168.9.100:2381,myetcd3=http://192.168.9.100:2382

etcd --name myetcd3  --listen-client-urls http://0.0.0.0:2399 --advertise-client-urls http://192.168.9.100:2399 --listen-peer-urls http://0.0.0.0:2382 --initial-advertise-peer-urls http://192.168.9.100:2382  --initial-cluster-token etcd-cluster-test --initial-cluster-state new --initial-cluster myetcd1=http://192.168.9.100:2380,myetcd2=http://192.168.9.100:2381,myetcd3=http://192.168.9.100:2382


etcd --name myetcd1
--listen-client-urls http://0.0.0.0:2379
--advertise-client-urls http://192.168.9.100:2379 
--listen-peer-urls http://0.0.0.0:2380 
--initial-advertise-peer-urls http://192.168.9.100:2380  
--initial-cluster-token etcd-cluster-test 
--initial-cluster-state new 
--initial-cluster myetcd1=http://192.168.9.100:2380,myetcd2=http://192.168.9.100:2381,myetcd3=http://192.168.9.100:2382

etcd 
--name myetcd2  
--listen-client-urls http://0.0.0.0:2389 
--advertise-client-urls http://192.168.9.100:2389 
--listen-peer-urls http://0.0.0.0:2381 
--initial-advertise-peer-urls http://192.168.9.100:2381  
--initial-cluster-token etcd-cluster-test 
--initial-cluster-state new 
--initial-cluster myetcd1=http://192.168.9.100:2380,myetcd2=http://192.168.9.100:2381,myetcd3=http://192.168.9.100:2382

etcd 
--name myetcd3
--listen-client-urls http://0.0.0.0:2399 
--advertise-client-urls http://192.168.9.100:2399 
--listen-peer-urls http://0.0.0.0:2382 
--initial-advertise-peer-urls http://192.168.9.100:2382  
--initial-cluster-token etcd-cluster-test 
--initial-cluster-state new 
--initial-cluster myetcd1=http://192.168.9.100:2380,myetcd2=http://192.168.9.100:2381,myetcd3=http://192.168.9.100:2382

----------------------------------------------------------------------------------------------------

flannel
flannel官网:https://coreos.com/flannel
项目地址:https://github.com/coreos/flannel


安装配置
https://www.hi-linux.com/posts/30481.html

----------------------------------------------------------------------------------------------------
servicemesh(比较火,很有潜力的开源项目)
lstio是基于servicemesh最新实现

官网:?

中文网：
http://www.servicemesh.cn/
----------------------------------------------------------------------------------------------------
kubernetes入门使用

查看kubectl版本
kubectl version

查看节点
kubectl get nodes

查看节点详细信息
kubectl describe node master.magedu.com(节点名称)

查看集群信息(6443)
kubectl cluster-info

创建一个pod
kubectl run nginx-deploy --image=nginx:1.14-alpine --port=80 --replicas=1
查看
kubectl get deployment
kubectl get pods
//查看更多信息,在哪个节点
kubectl get pods -o wide
//POD IP: 10.244.2.2 所属网卡:cni0	10.244.2.1

集群节点内部使用10.244.2.2访问(POD ip关闭,重启会改变的)
curl 10.244.2.2:80

发布service网络(固定不变的)
kubectl expose deployment nginx-deploy --name=nginx --port=80 --target-port=80 --protocol=TCP
service80           --port=80 
容器80              --target-port=80 
默认TCP可以不用指定 --protocol=TCP

查看发布的service(客户端节点,固定不变的)
	kubectl get services 
	or
	kubectl get svc
服务名称访问
	master执行: kubectl get pods -n kube-system -o wide
				kubectl get svc -n kube-system
	查看名称解析地址:dig -t A nginx @10.96.0.10 

	运行一个交互式容器(查看 wget http://nginx可以访问)
	kubectl run client --image=busybox --replicas=1 -it --restart=Never
	cat /etc/resolv.conf	//查看dns解析地址nameserver 10.96.0.10 search ...
	名称解析
	dig -t A nginx.default.svc.cluster.local @10.96.0.10 
	删除pod(固定service ip访问)
		kubectl delete pods nginx-deploy-xxxxxx-xxx
	查看service详细信息
		kubectl describe svc nginx
	编辑service IP
		kubectl edit svc nginx
		外网访问:type改成NodePort
	查看详细信息
	kubectl describe deployment nginx-deploy 

创建实例
	kubectl run myapp --image=ikubernets/myapp:v1 --replicas=2
	kubectl expose deployment myapp --name=myapp --port=80
	动态变动
	kubectl scale --replicas=5 deployment myapp
	kubectl scale --replicas=3 deployment myapp
滚动更新
	kubectl describe pods xxx 	//查看镜像版本
	//更新
	kubectl set images deployment myapp myapp=ikubernets/myapp:v2
	kubectl rollout status deployment myapp	//显示更新过程
	//回滚
	kubectl rollout undo deployment myapp
自动扩缩容
	需要配置监控系统

----------------------------------------------------------------------------------------------------
docker run --name gcr.azk8s.cn/google_containers/nginx:latest -p 8081:80 -d nginx
docker run --name my-nginx -p 8800:80 -it gcr.azk8s.cn/google_containers/nginx:latest /bin/bash

docker run --name my-centos -p 8800:80 -it centos-7-x86_64:latest /bin/bash
docker tag gcr.azk8s.cn/google_containers/nginx:latest nginx:1.14-alpine

https://www.zengjunpeng.com/?id=91
docker run --name my-nginx -p 8800:80 -it gcr.azk8s.cn/google_containers/nginx:latest /bin/bash
docker run --name my-centos -p 8801:80 -it centos-7-x86_64:latest nginx
不跟/bin/bash,run不起来

第一步：通过dig @114.114.114.114 registry-1.docker.io找到可用IP

执行生成nginx.repo,然后执行yum install nginx
rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

https://www.cnblogs.com/songxingzhu/p/8568432.html
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
----------------------------------------------------------------------------------------------------
问题
open /run/flannel/subnet.env: no such file or directory
https://blog.csdn.net/ANXIN997483092/article/details/86711006

删除node步骤，及加入节点注意事项
https://blog.csdn.net/weixin_34362991/article/details/93777614
```

