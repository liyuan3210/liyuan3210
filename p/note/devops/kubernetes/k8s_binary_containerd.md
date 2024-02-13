# kubernetes二进制高可用部署(Runtime Containerd)

##### 问题文档

```
1.下载：
kubernetes官网->右上角选择好version->选择Documentation->下方Download Kubernetes->选择左边“Release Notes”->选择“Changelog”->选中要下载的版本Changelog->就可以下载Client,Server,Node还有容器镜像了

2.组件启动参数：
kubernetes官网->右上角选择好version->选择Documentation->左菜单选择“Reference>Component Tools”->就可以查看每个组件的启动参数了

3.配置容器运行环境
https://kubernetes.io/docs/setup/production-environment/container-runtimes/

4.Customizing components with the kubeadm API
Kubernetes Documentation > Getting started > Production environment > Installing Kubernetes with deployment tools > Bootstrapping clusters with kubeadm > Customizing components with the kubeadm API

5.自己笔记参考：
https://gitee.com/liyuan3210/book_source/tree/master/k8s
```

##### kuernetes架构



##### 集群环境

```
集群机器规划
		node1 	node2 	node3 		node4 	node5 	node6 	node7
k8s:	master	master	master		node	node
etcd:					etcd		etcd	etcd
```

##### 软件版本

```
需要下载的docker
	1.kubernetes	v1.21.10
	2.etcd			v3.5.2
	3.calico		v3.19.4
		https://github.com/projectcalico/calico
	4.coredns		v1.8.4
		https://github.com/coredns/coredns
	5.docker		v20.10.13
	6.haproxy		v5.18	//版本是否有问题
	7.keepalived	v3.5	//版本是否有问题
	
需要下载的docker
	1.kubernetes	v1.21.10
	2.etcd			v3.5.2
	3.calico		v3.19.4
		https://github.com/projectcalico/calico
	4.coredns		v1.8.4
		https://github.com/coredns/coredns
	5.containerd	v20.10.13
	6.haproxy		v5.18	//版本是否有问题
	7.keepalived	v3.5	//版本是否有问题
	
当前实际下载版本
	1.kubernetes	v1.27.4
	2.etcd			v3.5.9
	3.calico		v3.26.1
		https://github.com/projectcalico/calico
	4.coredns		1.10.1
		https://github.com/coredns/coredns
	5.容器(根据实际情况来)
		docker		v24.0.5
		containerd	v24.0.5
	6.haproxy		v2.8.1
		http://www.haproxy.org/
	7.keepalived	v2.2.8
		https://www.keepalived.org/
```

##### 网络分配

```
Node网络:			node1地址
Service网络:		10.96.0.0/16
Pod网络:			10.244.0.0/16
```

##### linux文件存放目录

```
软件存放目录/opt：
	/opt/ssl			//公共ssl使用文件
	/opt/etcd			//etcd安装目录
		ssl
		cfg
		data
	/opt/kubernetes
		ssl
		cfg
		logs
```

##### 一。安装cfssl生成集群ca并安装etcd集群

###### 1.安装cfssl并生成集群证书

1.1）安装cfssl

```bash
wget https://pkg.cfssl.org/R1.2/cfssl_linux-amd64
wget https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
wget https://pkg.cfssl.org/R1.2/cfssl-certinfo_linux-amd64
chmod +x cfssl_linux-amd64 cfssljson_linux-amd64 cfssl-certinfo_linux-amd64
mv cfssl_linux-amd64 /usr/local/bin/cfssl
mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
mv cfssl-certinfo_linux-amd64 /usr/bin/cfssl-certinfo
```

1.2）生成集群证书

在node1节点创建/opt/ssl/并进入此目录执行如下命令

1.2.1）配置ca证书请求文件ca-csr.json

```json
cat > /opt/ssl/ca-csr.json<< EOF
{
  "CN": "kubernetes",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShangHai",
      "ST": "ShangHai",
	  "O": "liyuan3210",
	  "OU": "CN"
    }
  ],
  "ca":{
		"expiry": "87600h"
  }
}
EOF
```

1.2.2）创建ca证书

```bash
$ cfssl gencert -initca ca-csr.json | cfssljson -bare ca
# 会生成3个文件:证书请求文件，证书所对应key，证书本身（ca.csr ca-key.pem  ca.pem）
```

1.2.3）配置ca证书策略

```JSON
cat > /opt/ssl/ca-config.json<< EOF
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
#备注：
#	server auth表示client可以对使用该ca对server提供的证书进行验证
#	client auth表示可以使用该ca对client提供的证书进行验证
```

###### 2.etcd集群部署
2.1）解压etcd-v3.5.9-linux-amd64.tar.gz文件到/opt并重命名etcd在/opt/etcd
```bash
$ tar -xzvf etcd-v3.5.9-linux-amd64.tar.gz
$ mv etcd-v3.5.9-linux-amd64 /opt/etcd
```
2.2）/opt/etcd下创建需要的目录

```bash
$ mkdir /opt/etcd/ssl && mkdir /opt/etcd/cfg && mkdir /opt/etcd/data
```
2.3）进入/opt/etcd/ssl目录创建etcd证书请求源文件etcd-csr.json(host可以添加备用的)

```JSON
cat > /opt/etcd/ssl/etcd-csr.json<< EOF
{
  "CN": "etcd",
  "hosts": [
    "127.0.0.1",
    "192.168.56.107",
    "192.168.56.111",
    "192.168.56.112",
    "192.168.56.113",
    "192.168.56.114",
    "192.168.56.115",
    "192.168.56.116"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShangHai",
      "ST": "ShangHai",
      "O": "liyuan3210",
      "OU": "CN"
    }
  ]
}
EOF
```

2.4）生成证书（etcd.csr  etcd-key.pem  etcd.pem）

```bash
$ cfssl gencert -ca=/opt/ssl/ca.pem -ca-key=/opt/ssl/ca-key.pem -config=/opt/ssl/ca-config.json -profile=kubernetes etcd-csr.json | cfssljson -bare etcd
# 查看
$ ls etcd*pem
```

2.5）进入/opt/etcd/cfg目录创建启动配置文件（不同节点配置不同）

```bash
cat > /opt/etcd/cfg/etcd.conf << EOF
#[Member]
ETCD_NAME="etcd-1"
ETCD_DATA_DIR="/opt/etcd/data"
ETCD_LISTEN_PEER_URLS="https://192.168.56.112:2380"
ETCD_LISTEN_CLIENT_URLS="https://192.168.56.112:2379,https://127.0.0.1:2379"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.56.112:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.56.112:2379"
ETCD_INITIAL_CLUSTER="etcd-1=https://192.168.56.112:2380,etcd-2=https://192.168.56.113:2380,etcd-3=https://192.168.56.114:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF
```

2.6）创建服务启动文件etcd.service（每个节点都一样）

```bash
cat > /opt/etcd/cfg/etcd.service << EOF
[Unit]
Description=Etcd Server
After=network.target
After=network-online.target
Wants=network-online.target
[Service]
Type=notify
EnvironmentFile=/opt/etcd/cfg/etcd.conf
ExecStart=/opt/etcd/etcd \
--cert-file=/opt/etcd/ssl/etcd.pem \
--key-file=/opt/etcd/ssl/etcd-key.pem \
--peer-cert-file=/opt/etcd/ssl/etcd.pem \
--peer-key-file=/opt/etcd/ssl/etcd-key.pem \
--trusted-ca-file=/opt/ssl/ca.pem \
--peer-trusted-ca-file=/opt/ssl/ca.pem \
--peer-client-cert-auth \
--client-cert-auth \
--logger=zap
Restart=on-failure
RestartSec=5
LimitNOFILE=65536
[Install]
WantedBy=multi-user.target
EOF
```

2.7）安装包,ssl,配置文件同步到节点

```bash
# 同步集群证书文件/opt/ssl
scp -r /opt/ssl root@node3:/opt/
scp -r /opt/ssl root@node4:/opt/
scp -r /opt/ssl root@node5:/opt/

# 同步etcd 程序，ssl,cfg文件
scp -r /opt/etcd root@node3:/opt/
scp -r /opt/etcd root@node4:/opt/
scp -r /opt/etcd root@node5:/opt/

# 同步服务启动程序
scp /opt/etcd/cfg/etcd.service root@node3:/usr/lib/systemd/system/
scp /opt/etcd/cfg/etcd.service root@node4:/usr/lib/systemd/system/
scp /opt/etcd/cfg/etcd.service root@node5:/usr/lib/systemd/system/
```

注意要修改各个节点的/opt/etcd/cfg/etcd.conf文件

```bash
# scp发送到每个节点后，需要改的字段
ETCD_NAME							//节点名称，不能重复
ETCD_LISTEN_PEER_URLS				//当前节点ip
ETCD_LISTEN_CLIENT_URLS				//当前节点ip
ETCD_INITIAL_ADVERTISE_PEER_URLS	//当前节点ip
ETCD_ADVERTISE_CLIENT_URLS			//当前节点ip
ETCD_INITIAL_CLUSTER				//集群节点数组
```

2.8）启动各个节点

```
$ systemctl daemon-reload && systemctl enable --now etcd && systemctl status etcd
```

###### 3.ectd集群验证

3.1）根据key添加值与取值

```
添加值：
/opt/etcd/etcdctl --cacert=/opt/ssl/ca.pem \
--cert=/opt/etcd/ssl/etcd.pem \
--key=/opt/etcd/ssl/etcd-key.pem \
--endpoints https://192.168.56.113:2379 put foo2 hello
取值：
/opt/etcd/etcdctl --cacert=/opt/ssl/ca.pem \
--cert=/opt/etcd/ssl/etcd.pem \
--key=/opt/etcd/ssl/etcd-key.pem \
--endpoints https://192.168.56.114:2379 get foo2 hello
```

3.1）etcd健康检查

```bash
# 健康检查
$ ETCDCTL_API=3 /opt/etcd/etcdctl --write-out=table --cacert=/opt/ssl/ca.pem --cert=/opt/etcd/ssl/etcd.pem --key=/opt/etcd/ssl/etcd-key.pem --endpoints="https://192.168.56.112:2379,https://192.168.56.113:2379,https://192.168.56.114:2379" endpoint health

# 性能检查
$ ETCDCTL_API=3 /opt/etcd/etcdctl --write-out=table --cacert=/opt/ssl/ca.pem --cert=/opt/etcd/ssl/etcd.pem --key=/opt/etcd/ssl/etcd-key.pem --endpoints=https://192.168.56.112:2379,https://192.168.56.113:2379,https://192.168.56.114:2379 check perf
```

##### 二。安装kubernetes主节点(master)

###### 1.软件包分发及目录创建

1.1).解压kubernetes-server-linux-amd64.tar.gz并进入../server/bin目录下

```bash
$ tar -xzvf kubernetes-server-linux-amd64.tar.gz
```

1.2).根据节点规划分发二进制文件

```bash
# master节点:
scp kube-apiserver kube-controller-manager kube-scheduler kubectl node1:/usr/local/bin
scp kube-apiserver kube-controller-manager kube-scheduler kubectl node2:/usr/local/bin
scp kube-apiserver kube-controller-manager kube-scheduler kubectl node3:/usr/local/bin
# node节点：
scp kubelet kube-proxy node4:/usr/local/bin
scp kubelet kube-proxy node5:/usr/local/bin
```

1.3).创建需要的目录

```bash
$ mkdir /opt/kubernetes&&mkdir /opt/kubernetes/ssl&&mkdir /opt/kubernetes/logs&&mkdir /opt/kubernetes/cfg
```

###### 2.安装部署kube-apiserver

2.1）.进入/opt/kubernetes/ssl目录，创建kube-apiserver-csr.json请求文件

```json
cat > /opt/kubernetes/ssl/kube-apiserver-csr.json<< EOF
{
  "CN": "kubernetes",
  "hosts": [
    "127.0.0.1",
    "192.168.56.107",
    "192.168.56.111",
    "192.168.56.112",
	"192.168.56.113",
	"192.168.56.114",
	"192.168.56.115",
	"192.168.56.116",
	"192.168.56.117",
	"192.168.56.118",
	"192.168.56.119",
	"192.168.56.120",
	"10.96.0.1",
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
      "L": "ShangHai",
      "ST": "ShangHai",
      "O": "liyuan3210",
      "OU": "CN"
    }
  ]
}
EOF
# 注意：
#	上面的10.96.0.1是service网络，还有下面预留网段
```

2.2）.生成apiserver证书

```bash
$ cfssl gencert -ca=/opt/ssl/ca.pem -ca-key=/opt/ssl/ca-key.pem -config=/opt/ssl/ca-config.json -profile=kubernetes kube-apiserver-csr.json | cfssljson -bare kube-apiserver
$ ls kube-apiserver*pem	//查看
```

2.3）.生成token文件

```bash
$ cat > /opt/kubernetes/ssl/token.csv << EOF
$(head -c 16 /dev/urandom | od -An -t x | tr -d ' '),kubelet-bootstrap,10001,"system:kubelet-bootstrap"
EOF
# 说明：
# 创建TLS机制所需要token
# TLS bootstraping:Master apiserver启用TLS认证后，Node节点kubelet和kube-proxy与kube-apiserver进行通信，
# 必须使用CA签发的有效证书才可以，当node节点很多时，这种客户端证书颁发需要大量工作，同样也会增加集群扩展复杂度。
# 为了简化流程，kubernetes引入了TLS bootstraping机制来自动颁发客户端证书，kubelet会以一个低权限用户自动向apiserver申请证书，
# kubelet的证书由apiserver动态签署。所以强烈建议node上使用这种方式，目前主要用于kubelet,kube-proxy还是由我们统一发一个证书。
```

2.4）.创建kube-apiserver.conf配置文件（不同节点配置不同）

```bash
# kubernetes-v1.27.4版本
cat > /opt/kubernetes/cfg/kube-apiserver.conf << "EOF"
KUBE_APISERVER_OPTS="--enable-admission-plugins=NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota \
  --anonymous-auth=false \
  --bind-address=192.168.56.107 \
  --secure-port=6443 \
  --advertise-address=192.168.56.107 \
  --authorization-mode=Node,RBAC \
  --runtime-config=api/all=true \
  --enable-bootstrap-token-auth \
  --service-cluster-ip-range=10.96.0.0/16 \
  --token-auth-file=/opt/kubernetes/ssl/token.csv \
  --service-node-port-range=30000-32767 \
  --tls-cert-file=/opt/kubernetes/ssl/kube-apiserver.pem  \
  --tls-private-key-file=/opt/kubernetes/ssl/kube-apiserver-key.pem \
  --client-ca-file=/opt/ssl/ca.pem \
  --kubelet-client-certificate=/opt/kubernetes/ssl/kube-apiserver.pem \
  --kubelet-client-key=/opt/kubernetes/ssl/kube-apiserver-key.pem \
  --service-account-key-file=/opt/ssl/ca-key.pem \
  --service-account-signing-key-file=/opt/ssl/ca-key.pem  \
  --service-account-issuer=api \
  --etcd-cafile=/opt/ssl/ca.pem \
  --etcd-certfile=/opt/etcd/ssl/etcd.pem \
  --etcd-keyfile=/opt/etcd/ssl/etcd-key.pem \
  --etcd-servers=https://192.168.56.112:2379,https://192.168.56.113:2379,https://192.168.56.114:2379 \
  --allow-privileged=true \
  --apiserver-count=3 \
  --audit-log-maxage=30 \
  --audit-log-maxbackup=3 \
  --audit-log-maxsize=100 \
  --audit-log-path=/opt/kubernetes/logs/kube-apiserver-audit.log \
  --event-ttl=1h \
  --v=4"
EOF
# 不认识的标识（最新k8s版本）
  --insecure-port=0 \
  https://zhuanlan.zhihu.com/p/598823122
  --enable-swagger-ui=true \
  https://blog.csdn.net/yuezhilangniao/article/details/120171212
  --alsologtostderr=true \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/logs/kube-apiserver \

# kubernetes-v1.21.14版本
cat > /opt/kubernetes/cfg/kube-apiserver.conf << "EOF"
KUBE_APISERVER_OPTS="--enable-admission-plugins=NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota \
  --anonymous-auth=false \
  --bind-address=192.168.56.107 \
  --secure-port=6443 \
  --advertise-address=192.168.56.107 \
  --insecure-port=0 \
  --authorization-mode=Node,RBAC \
  --runtime-config=api/all=true \
  --enable-bootstrap-token-auth \
  --service-cluster-ip-range=10.96.0.0/16 \
  --token-auth-file=/opt/kubernetes/ssl/token.csv \
  --service-node-port-range=30000-32767 \
  --tls-cert-file=/opt/kubernetes/ssl/kube-apiserver.pem  \
  --tls-private-key-file=/opt/kubernetes/ssl/kube-apiserver-key.pem \
  --client-ca-file=/opt/ssl/ca.pem \
  --kubelet-client-certificate=/opt/kubernetes/ssl/kube-apiserver.pem \
  --kubelet-client-key=/opt/kubernetes/ssl/kube-apiserver-key.pem \
  --service-account-key-file=/opt/ssl/ca-key.pem \
  --service-account-signing-key-file=/opt/ssl/ca-key.pem  \
  --service-account-issuer=api \
  --etcd-cafile=/opt/ssl/ca.pem \
  --etcd-certfile=/opt/etcd/ssl/etcd.pem \
  --etcd-keyfile=/opt/etcd/ssl/etcd-key.pem \
  --etcd-servers=https://192.168.56.112:2379,https://192.168.56.113:2379,https://192.168.56.114:2379 \
  --enable-swagger-ui=true \
  --allow-privileged=true \
  --apiserver-count=3 \
  --audit-log-maxage=30 \
  --audit-log-maxbackup=3 \
  --audit-log-maxsize=100 \
  --audit-log-path=/opt/kubernetes/logs/kube-apiserver-audit.log \
  --event-ttl=1h \
  --alsologtostderr=true \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/logs \
  --v=4"
EOF
```

2.5）.创建kube-apiserver服务启动文件

```bash
cat > /opt/kubernetes/cfg/kube-apiserver.service << EOF
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes
After=etcd.service
Wants=etcd.service

[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-apiserver.conf
ExecStart=/usr/local/bin/kube-apiserver \$KUBE_APISERVER_OPTS
Restart=on-failure
RestartSec=5
Type=notify
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF
```

2.6）.分发到各节点并启动

```bash
# 拷贝kubernetes文件
scp -r /opt/kubernetes root@node2:/opt
scp -r /opt/kubernetes root@node3:/opt

# 拷贝服务启动文件
cp /opt/kubernetes/cfg/kube-apiserver.service /usr/lib/systemd/system/
scp /opt/kubernetes/cfg/kube-apiserver.service root@node2:/usr/lib/systemd/system/
scp /opt/kubernetes/cfg/kube-apiserver.service root@node3:/usr/lib/systemd/system/

# 拷贝/opt/ssl信息（保证master节点都有/opt/ssl证书文件）
scp -r /opt/ssl root@node2:/opt/ssl
# 拷贝/opt/etcd/ssl信息（保证master节点都有/opt/etcd/ssl证书文件）
scp -r /opt/etcd/ssl root@node2:/opt/etcd/ssl
```

修改各个节点kube-apiserver.conf文件

```
# 不同节点需要改的字段
# bind-address //主节点Ip
# advertise-address //主节点Ip
```

启动

```
systemctl daemon-reload && systemctl enable --now kube-apiserver && systemctl status kube-apiserver
```

**使用journalctl -xe查看启动错误信息**

###### 3.部署kubectl

3.1）创建kubectl证书请求文件

```json
cat > /opt/kubernetes/ssl/admin-csr.json<< EOF
{
  "CN": "admin",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShangHai",
      "ST": "ShangHai",
      "O": "system:masters",
      "OU": "system"
    }
  ]
}
EOF
# 说明：
# 	后续kube-apiserver使用RBAC对客户端（如kubelet,kube-proxy,pod）请求进行授权.
# 	kube-apiserver预定义了一些RBAC使用的RoleBindings,如cluster-admin将Group system:masters与Role Cluster-admin绑定.
# 	该Role授予了调用kube-apiserver的所有API的权限.
# 	上面文件"O"指定该证书的Group为system:masters,kubelet使用该证书访问kube-apiserver时，由于证书被CA签名，所以认证通过，同时由于证书用户组经过预授权的system:masters,所以被授予访问所有的API的权限；
# 	注:
# 	这个admin证书，是将来生成管理员用的kubeconfig配置文件用的，现在我们一般建议使用RBAC来对kubernetes进行角色权限控制，
# 	kubernetes将证书中的CN字段作为user,O字段作为Group；
# 	"O"："system:masters",必须是system:masters，否则后面kubectl create clusterrolebinding报错。
```

3.2）生成证书

```
$ cfssl gencert -ca=/opt/ssl/ca.pem -ca-key=/opt/ssl/ca-key.pem -config=/opt/ssl/ca-config.json -profile=kubernetes admin-csr.json | cfssljson -bare admin
$ ls admin*pem	//查看
```

3.3）生成kubeconfig配置文件(总共四步，生成kube.config文件)

```bash
# kube.config为kubectl的配置文件，包含访问apiserver的所有信息，如apiserver地址，CA证书和自身使用的证书
//进入cfg目录
$ cd /opt/kubernetes/cfg/
//配置哪个集群,证书
$ kubectl config set-cluster kubernetes --certificate-authority=/opt/ssl/ca.pem --embed-certs=true --server=https://192.168.56.107:6443 --kubeconfig=kube.config
//证书角色管理员
$ kubectl config set-credentials admin --client-certificate=/opt/kubernetes/ssl/admin.pem --client-key=/opt/kubernetes/ssl/admin-key.pem --embed-certs=true --kubeconfig=/opt/kubernetes/cfg/kube.config
//设置安全上下文
$ kubectl config set-context kubernetes --cluster=kubernetes --user=admin --kubeconfig=kube.config
//设置安全上下文
$ kubectl config use-context kubernetes --kubeconfig=kube.config
```

3.4）准备kubectl配置文件并进行角色绑定

```bash
$ mkdir ~/.kube
$ cp kube.config ~/.kube/config
# 下面执行有问题
# error: failed to create clusterrolebinding: Post "http://localhost:8080/apis/rbac.authorization.k8s.io/v1/clusterrolebindings?fieldManager=kubectl-create&fieldValidation=Strict": dial tcp [::1]:8080: connect: connection refused
$ kubectl create clusterrolebinding kube-apiserver:kubelete-apis --clusterrole=system:kubelet-api-admin --user kubernetes --kubeconfig=/root/.kube/config
```

3.5)查看集群状态

```bash
export KUBECONFIG=$HOME/.kube/config

查看集群信息
kubectl cluster-info

查看集群组件状态
kubectl get componentstatuses

查看命名空间中资源对象
kubectl get all --all-namespaces
```

3.6)同步到剩下master节点

```
在node2,node3执行:
$ mkdir /root/.kube
拷贝：
scp /root/.kube/config node2:/root/.kube/config
scp /root/.kube/config node3:/root/.kube/config
```

###### 4.部署kube-controller-manager

4.1）创建kube-controller-manager证书请求文件

```json
cat > /opt/kubernetes/ssl/kube-controller-manager-csr.json<< EOF
{
  "CN": "system:kube-controller-manager",
  "hosts": [
	"127.0.0.1",
    "192.168.56.107",
    "192.168.56.111",
    "192.168.56.112",
	"192.168.56.113",
	"192.168.56.114"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShangHai",
      "ST": "ShangHai",
      "O": "system:kube-controller-manager",
      "OU": "system"
    }
  ]
}
EOF
# 说明：
#	hosts 列包含所有kube-controller-manager节点ip
#	CN为system:kube-controller-manager；
#	O为system:kube-controller-manager，kubernetes内置的ClusterRoleBindings system:kube-controller-manager赋予kube-controller-manager工作所需权限
```

4.2）.创建kube-controller-manager证书文件

```bash
$ cfssl gencert -ca=/opt/ssl/ca.pem -ca-key=/opt/ssl/ca-key.pem -config=/opt/ssl/ca-config.json -profile=kubernetes kube-controller-manager-csr.json | cfssljson -bare kube-controller-manager
$ ls kube-controller-manager*pem	//查看
```

4.3）.创建kube-controller-manager.kubeconfig文件(如果需要在节点实现控制器管理，就需要此步创建)

```bash
//进入cfg目录
$ cd /opt/kubernetes/cfg/
//配置哪个集群,证书
$ kubectl config set-cluster kubernetes --certificate-authority=/opt/ssl/ca.pem --embed-certs=true --server=https://192.168.56.107:6443 --kubeconfig=kube-controller-manager.kubeconfig
//证书角色管理员
$ kubectl config set-credentials system:kube-controller-manager --client-certificate=/opt/kubernetes/ssl/kube-controller-manager.pem --client-key=/opt/kubernetes/ssl/kube-controller-manager-key.pem --embed-certs=true --kubeconfig=/opt/kubernetes/cfg/kube-controller-manager.kubeconfig
//设置安全上下文
$ kubectl config set-context system:kube-controller-manager --cluster=kubernetes --user=system:kube-controller-manager --kubeconfig=kube-controller-manager.kubeconfig
//设置安全上下文
$ kubectl config use-context system:kube-controller-manager --kubeconfig=kube-controller-manager.kubeconfig
# 注意：需要同步到各个master节点
```

4.4）.创建kube-controller-manager.conf配置文件

```bash
# kubernetes-v1.27.4版本
cat > /opt/kubernetes/cfg/kube-controller-manager.conf << "EOF"
KUBE_CONTROLLER_MANAGER_OPTS="--port=10252 \
  --secure-port=10257 \
  --bind-address=127.0.0.1 \
  --kubeconfig=/opt/kubernetes/cfg/kube-controller-manager.kubeconfig \
  --service-cluster-ip-range=10.96.0.0/16 \
  --cluster-name=kubernetes \
  --cluster-signing-cert-file=/opt/ssl/ca.pem \
  --cluster-signing-key-file=/opt/ssl/ca-key.pem \
  --allocate-node-cidrs=true \
  --cluster-cidr=10.244.0.0/16 \
  --experimental-cluster-signing-duration=87600h \
  --root-ca-file=/opt/ssl/ca.pem \
  --service-account-private-key-file=/opt/ssl/ca-key.pem \
  --leader-elect=true \
  --feature-gates=RotateKubeletServerCertificate=true \
  --controllers=*,bootstrapsigner,tokencleaner \
  --horizontal-pod-autoscaler-use-rest-clients=true \
  --horizontal-pod-autoscaler-sync-period=10s \
  --tls-cert-file=/opt/kubernetes/ssl/kube-controller-manager.pem \
  --tls-private-key-file=/opt/kubernetes/ssl/kube-controller-manager-key.pem \
  --use-service-account-credentials=true \
  --alsologtostderr=true \
  --logtostderr=false \
  --v=2"
EOF

# 不认识的标识（最新k8s版本）
--port=10252
--experimental-cluster-signing-duration=87600h
--horizontal-pod-autoscaler-use-rest-clients=true
--alsologtostderr=true
--logtostderr=false
--log-dir=/opt/kubernetes/logs

# kubernetes-v1.21.14版本
cat > /opt/kubernetes/cfg/kube-controller-manager.conf << "EOF"
KUBE_CONTROLLER_MANAGER_OPTS="--port=10252 \
  --secure-port=10257 \
  --bind-address=127.0.0.1 \
  --master=127.0.0.1:8080 \
  --kubeconfig=/opt/kubernetes/cfg/kube-controller-manager.kubeconfig \
  --service-cluster-ip-range=10.96.0.0/16 \
  --cluster-name=kubernetes \
  --cluster-signing-cert-file=/opt/ssl/ca.pem \
  --cluster-signing-key-file=/opt/ssl/ca-key.pem \
  --allocate-node-cidrs=true \
  --cluster-cidr=10.244.0.0/16 \
  --experimental-cluster-signing-duration=87600h \
  --root-ca-file=/etc/kubernetes/ssl/ca.pem \
  --service-account-private-key-file=/opt/ssl/ca-key.pem \
  --leader-elect=true \
  --feature-gates=RotateKubeletServerCertificate=true \
  --controllers=*,bootstrapsigner,tokencleaner \
  --horizontal-pod-autoscaler-use-rest-clients=true \
  --horizontal-pod-autoscaler-sync-period=10s \
  --tls-cert-file=/opt/kubernetes/ssl/kube-controller-manager.pem \
  --tls-private-key-file=/opt/kubernetes/ssl/kube-controller-manager-key.pem \
  --use-service-account-credentials=true \
  --alsologtostderr=true \
  --logtostderr=false \
  --log-dir=/opt/kubernetes/logs \
  --v=2"
EOF
#注意：
#	与视频教程相比需要加上”--master=127.0.0.1:8080 \“ 项才可启动成功，否则报如下问题
#   no configuration has been provided, try setting KUBERNETES_MASTER environment variable
```

4.5）.创建kube-controller-manager.service服务启动文件

```bash
cat > /opt/kubernetes/cfg/kube-controller-manager.service << "EOF"
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes

[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-controller-manager.conf
ExecStart=/usr/local/bin/kube-controller-manager \$KUBE_CONTROLLER_MANAGER_OPTS
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

4.6）.分发到各个master服务器并启动

```bash
查看证书
$ openssl x509 -in /opt/kubernetes/ssl/kube-controller-manager.pem -noout -text

文件同步
scp /opt/kubernetes/ssl/* root@node2:/opt/kubernetes/ssl/
scp /opt/kubernetes/cfg/* root@node2:/opt/kubernetes/cfg/
scp /opt/kubernetes/cfg/kube-controller-manager.service root@node2:/usr/lib/systemd/system/
```

服务启动

```bash
$ systemctl daemon-reload && systemctl enable --now kube-controller-manager && systemctl status kube-controller-manager
```

**使用journalctl -xe查看启动错误信息**

查看状态

```bash
$ kubectl get componentstatuses
```

###### 5.部署kube-scheduler

5.1）创建kube-scheduler-csr.json请求文件

```json
cat >  /opt/kubernetes/ssl/kube-scheduler-csr.json<< EOF
{
  "CN": "system:kube-scheduler",
  "hosts": [
	"127.0.0.1",
    "192.168.56.107",
    "192.168.56.111",
    "192.168.56.112",
	"192.168.56.113",
	"192.168.56.114"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "ShangHai",
      "ST": "ShangHai",
      "O": "system:kube-scheduler",
      "OU": "system"
    }
  ]
}
EOF
```

5.2）创建kube-scheduler证书文件

```bash
$ cfssl gencert -ca=/opt/ssl/ca.pem -ca-key=/opt/ssl/ca-key.pem -config=/opt/ssl/ca-config.json -profile=kubernetes kube-scheduler-csr.json | cfssljson -bare kube-scheduler
$ ls kube-scheduler*pem	//查看
```

5.3）创建kube-scheduler的kubeconfig

```bash
//进入cfg目录
$ cd /opt/kubernetes/cfg/
// 配置哪个集群,证书
kubectl config set-cluster kubernetes --certificate-authority=/opt/ssl/ca.pem --embed-certs=true --server=https://192.168.10.100:6443 --kubeconfig=kube-scheduler.kubeconfig
// 证书角色管理员
kubectl config set-credentials system:kube-scheduler --client-certificate=/opt/kubernetes/ssl/kube-scheduler.pem --client-key=/opt/kubernetes/ssl/kube-scheduler-key.pem --embed-certs=true --kubeconfig=/opt/kubernetes/cfg/kube-scheduler.kubeconfig
//设置安全上下文
kubectl config set-context system:kube-scheduler --cluster=kubernetes --user=system:kube-scheduler --kubeconfig=kube-scheduler.kubeconfig
// 设置安全上下文
kubectl config use-context system:kube-scheduler --kubeconfig=kube-scheduler.kubeconfig
```

5.4）.创建服务配置文件kube-scheduler.conf

```bash
cat > /opt/kubernetes/cfg/kube-scheduler.conf << EOF
KUBE_SCHEDULER_OPTS="--address=127.0.0.1 \
--kubeconfig=/opt/kubernetes/cfg/kube-scheduler.kubeconfig \
--leader-elect=true \
--alsologtostderr=true \
--logtostderr=false \
--log-dir=/opt/kubernetes/logs \
--v=2"
EOF
```

5.5）.创建服务启动文件kube-scheduler.service

```bash
cat > /opt/kubernetes/cfg/kube-scheduler.service << EOF
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes
[Service]
EnvironmentFile=/opt/kubernetes/cfg/kube-scheduler.conf
ExecStart=/usr/local/bin/kube-scheduler \$KUBE_SCHEDULER_OPTS
Restart=on-failure
RestartSec=5
[Install]
WantedBy=multi-user.target
EOF
```

5.6）.考本文件到各个节点并启动

```bash
文件同步
scp /opt/kubernetes/ssl/* root@node2:/opt/kubernetes/ssl/
scp /opt/kubernetes/cfg/* root@node2:/opt/kubernetes/cfg/
scp /opt/kubernetes/cfg/kube-scheduler.service root@node2:/usr/lib/systemd/system/

systemctl daemon-reload
systemctl enable --now kube-scheduler
systemctl status kube-scheduler
$ systemctl daemon-reload && systemctl enable --now kube-scheduler && systemctl status kube-scheduler
```

##### 三.安装kubernetes工作节点(worker)

###### 1.下载安装containerd

1.1）下载containerd安装包

https://github.com/containerd/containerd

需要下载cri-containerd-cni版本的(包含cri,cni组件),ri-containerd-cni-1.7.3-linux-amd64.tar.gz

1.2）解压安装(解压到跟目录/,因为包含了etc,opt,usr)

```bash
$ tar -xf cri-containerd-cni-1.7.3-linux-amd64.tar.gz -C /

# 解压后的目录
/etc
/opt
/usr

#验证
$ crictl ps
```

1.2）生成配置文件并修改

```bash
创建containerd目录
$ mkdir /etc/containerd

默认生成配置文件
$ containerd config default > /etc/containerd/config.toml

下面仅默认生成的文件需要执行
sed -i 's@systemd_cgroup = false@systemd_cgroup = true@' /etc/containerd/config.toml
sed -i 's@k8s.gcr.io/pause:3.6@registry.aliyuncs.com/google_containers/pause:3.6@' /etc/containerd/config.toml

生成默认配置文件需要修改的点
oom_score = 0 			    改为 		oom_score = -999
sandbox_image = "registry.k8s.io/pause:3.8"		改为如下
sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.8"
[plugins]
  systemd_cgroup = false    改为 		systemd_cgroup = true
[plugins."io.containerd.grpc.v1.cri".cni]
  conf_template = ""		改为		conf_template = "/etc/cni/net.d/10-default.conf"
[plugins]					改为(下面添加如下)
。。。。。。
[plugins.cri.registry]（每一级，两个空格）
      [plugins.cri.registry.mirrors]
        [plugins.cri.registry.mirrors."docker.io"]
          endpoint = [
            "https://docker.mirrors.ustc.edu.cn",
            "http://hub-mirror.c.163.com"
          ]
        [plugins.cri.registry.mirrors."gcr.io"]
          endpoint = [
            "https://gcr.mirrors.ustc.edu.cn"
          ]
        [plugins.cri.registry.mirrors."k8s.gcr.io"]
          endpoint = [
            "https://gcr.mirrors.ustc.edu.cn/google-containers/"
          ]
        [plugins.cri.registry.mirrors."quay.io"]
          endpoint = [
            "https://quay.mirrors.ustc.edu.cn"
          ]
        [plugins.cri.registry.mirrors."harbor.kubemsb.com"]
          endpoint = [
            "http://harbor.kubemsb.com"
          ]

三方镜像源配置：
[plugins]
  。。。。。。
  [plugins."io.containerd.grpc.v1.cri".registry]
    。。。。。。
    [plugins."io.containerd.grpc.v1.cri".registry.mirrors] #主要在这个下面配置

        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
          endpoint = [
            "https://docker.mirrors.ustc.edu.cn",
            "http://hub-mirror.c.163.com"
          ]
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."gcr.io"]
          endpoint = [
            "https://gcr.mirrors.ustc.edu.cn"
          ]
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."k8s.gcr.io"]
          endpoint = [
            "https://gcr.mirrors.ustc.edu.cn/google-containers/"
          ]
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."quay.io"]
          endpoint = [
            "https://quay.mirrors.ustc.edu.cn"
          ]
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."harbor.kubemsb.com"]
          endpoint = [
            "http://harbor.kubemsb.com"
          ]		
```

实际/etc/containerd/config.toml内容(可直接使用这个文件，省去上面生成默认与sed步骤)：

```bash
cat >/etc/containerd/config.toml<<EOF
root = "/var/lib/containerd"
state = "/run/containerd"
oom_score = -999

[grpc]
  address = "/run/containerd/containerd.sock"
  uid = 0
  gid = 0
  max_recv_message_size = 16777216
  max_send_message_size = 16777216

[debug]
  address = ""
  uid = 0
  gid = 0
  level = ""

[metrics]
  address = ""
  grpc_histogram = false

[cgroup]
  path = ""

[plugins]
  [plugins.cgroups]
    no_prometheus = false
  [plugins.cri]
    stream_server_address = "127.0.0.1"
    stream_server_port = "0"
    enable_selinux = false
    sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.6"
    stats_collect_period = 10
    systemd_cgroup = true
    enable_tls_streaming = false
    max_container_log_line_size = 16384
    [plugins.cri.containerd]
      snapshotter = "overlayfs"
      no_pivot = false
      [plugins.cri.containerd.default_runtime]
        runtime_type = "io.containerd.runtime.v1.linux"
        runtime_engine = ""
        runtime_root = ""
      [plugins.cri.containerd.untrusted_workload_runtime]
        runtime_type = ""
        runtime_engine = ""
        runtime_root = ""
    [plugins.cri.cni]
      bin_dir = "/opt/cni/bin"
      conf_dir = "/etc/cni/net.d"
      conf_template = "/etc/cni/net.d/10-default.conf"
    [plugins.cri.registry]
      [plugins.cri.registry.mirrors]
        [plugins.cri.registry.mirrors."docker.io"]
          endpoint = [
            "https://docker.mirrors.ustc.edu.cn",
            "http://hub-mirror.c.163.com"
          ]
        [plugins.cri.registry.mirrors."gcr.io"]
          endpoint = [
            "https://gcr.mirrors.ustc.edu.cn"
          ]
        [plugins.cri.registry.mirrors."k8s.gcr.io"]
          endpoint = [
            "https://gcr.mirrors.ustc.edu.cn/google-containers/"
          ]
        [plugins.cri.registry.mirrors."quay.io"]
          endpoint = [
            "https://quay.mirrors.ustc.edu.cn"
          ]
        [plugins.cri.registry.mirrors."harbor.kubemsb.com"]
          endpoint = [
            "http://harbor.kubemsb.com"
          ]
    [plugins.cri.x509_key_pair_streaming]
      tls_cert_file = ""
      tls_key_file = ""
  [plugins.diff-service]
    default = ["walking"]
  [plugins.linux]
    shim = "containerd-shim"
    runtime = "runc"
    runtime_root = ""
    no_shim = false
    shim_debug = false
  [plugins.opt]
    path = "/opt/containerd"
  [plugins.restart]
    interval = "10s"
  [plugins.scheduler]
    pause_threshold = 0.02
    deletion_threshold = 0
    mutation_threshold = 100
    schedule_delay = "0s"
    startup_delay = "100ms"
EOF
```

1.3）下载 runc（可以使用which runc查看替换位置）

进行下载:
https://github.com/opencontainers/runc

```
$ chmod +x runc.amd64
$ mv runc.amd64 /usr/local/sbin/runc
```

1.4）启动containerd

```
$ systemctl enable --now containerd		//启动
$ systemctl status containerd			//查看状态
```

###### 2.部署kubelet(只有containerd才需要此步骤，docker可以忽略)

2.1）创建kubelet-bootstrap.kubeconfig

```bash
//进入cfg目录
$ cd /opt/kubernetes/cfg/
# 取出csv文件token值,cd /opt/kubernetes/ssl/
BOOTSTRAP_TOKEN=$(awk -F "," '{print $1}' /opt/kubernetes/ssl/token.csv)

kubectl config set-cluster kubernetes --certificate-authority=/opt/ssl/ca.pem --embed-certs=true --server=https://192.168.56.107:6443 --kubeconfig=/opt/kubernetes/cfg/kubelet-bootstrap.kubeconfig

kubectl config set-credentials kubelet-bootstrap --token=${BOOTSTRAP_TOKEN} --kubeconfig=kubelet-bootstrap.kubeconfig

kubectl config set-context default --cluster=kubernetes --user=kubelet-bootstrap --kubeconfig=/opt/kubernetes/cfg/kubelet-bootstrap.kubeconfig

kubectl config use-context default --kubeconfig=kubelet-bootstrap.kubeconfig
```

角色绑定

```bash
$ kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --user=kubelet-bootstrap

$ kubectl create clusterrolebinding kubelet-bootstrap --clusterrole=system:node-bootstrapper --user=kubelet-bootstrap --kubeconfig=/opt/kubernetes/cfg/kubelet-bootstrap.kubeconfig
```

验证

```bash
$ kubectl describe clusterrolebinding cluster-system-anonymous

$ kubectl describe clusterrolebinding kubelet-bootstrap
```

2.2）创建kubelet配置文件(不同节点配置有所不同)

```json
cat > /opt/kubernetes/cfg/kubelet.json << "EOF"
{
  "kind": "KubeletConfiguration",
  "apiVersion": "kubelet.config.k8s.io/v1beta1",
  "authentication": {
    "x509": {
      "clientCAFile": "/opt/ssl/ca.pem"
    },
    "webhook": {
      "enabled": true,
      "cacheTTL": "2m0s"
    },
    "anonymous": {
      "enabled": false
    }
  },
  "authorization": {
    "mode": "Webhook",
    "webhook": {
      "cacheAuthorizedTTL": "5m0s",
      "cacheUnauthorizedTTL": "30s"
    }
  },
  "address": "192.168.56.107",
  "port": 10250,
  "readOnlyPort": 10255,
  "cgroupDriver": "systemd",                    
  "hairpinMode": "promiscuous-bridge",
  "serializeImagePulls": false,
  "clusterDomain": "cluster.local.",
  "clusterDNS": ["10.96.0.2"]
}
EOF

#说明：
#	kubelet.json中address需要修改为当前主机IP地址。
```

2.3）创建kubelet服务启动文件

```bash
cat > /opt/kubernetes/cfg/kubelet.service << "EOF"
[Unit]
Description=Kubernetes Kubelet
Documentation=https://github.com/kubernetes/kubernetes
After=containerd.service
Requires=containerd.service

[Service]
WorkingDirectory=/var/lib/kubelet
ExecStart=/usr/local/bin/kubelet \
  --bootstrap-kubeconfig=/opt/kubernetes/cfg/kubelet-bootstrap.kubeconfig \
  --cert-dir=/opt/kubernetes/ssl \
  --kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig \
  --config=/opt/kubernetes/cfg/kubelet.json \
  --cni-bin-dir=/opt/cni/bin \
  --cni-conf-dir=/etc/cni/net.d \
  --container-runtime=remote \
  --container-runtime-endpoint=unix:///run/containerd/containerd.sock \
  --network-plugin=cni \
  --rotate-certificates \
  --pod-infra-container-image=registry.aliyuncs.com/google_containers/pause:3.2 \
  --root-dir=/etc/cni/net.d \
  --alsologtostderr=true \
  --logtostderr=false \
  --log-dir=/var/log/kubernetes \
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

2.4）同步到集群节点

```bash
cp kubelet-bootstrap.kubeconfig /etc/kubernetes/
cp kubelet.json /etc/kubernetes/
cp kubelet.service /usr/lib/systemd/system/

for i in  k8s-master2 k8s-master3 k8s-worker1;do scp kubelet-bootstrap.kubeconfig kubelet.json $i:/etc/kubernetes/;done
for i in  k8s-master2 k8s-master3 k8s-worker1;do scp ca.pem $i:/etc/kubernetes/ssl/;done
for i in k8s-master2 k8s-master3 k8s-worker1;do scp kubelet.service $i:/usr/lib/systemd/system/;done
```

2.5）启动

```bash
创建目录（可能要创建）
mkdir -p /var/lib/kubelet
mkdir -p /var/log/kubernetes
	
systemctl daemon-reload
systemctl enable --now kubelet
```

2.6）检查

```bash
$ kubectl get nodes
$ kubectl get csr
```

###### 3.部署kube-proxy

3.1）创建kube-proxy证书请求文件

```bash
cat > /opt/kubernetes/ssl/kube-proxy-csr.json << "EOF"
{
  "CN": "system:kube-proxy",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "ST": "Beijing",
      "L": "Beijing",
      "O": "kubemsb",
      "OU": "CN"
    }
  ]
}
EOF
```

3.2）生成证书

```bash
$ cfssl gencert -ca=/opt/ssl/ca.pem -ca-key=/opt/ssl/ca-key.pem -config=/opt/ssl/ca-config.json -profile=kubernetes kube-proxy-csr.json | cfssljson -bare kube-proxy
$ ls kube-proxy*pem	//查看
```

3.3）创建kubeconfig文件

```bash
kubectl config set-cluster kubernetes --certificate-authority=/opt/ssl/ca.pem --embed-certs=true --server=https://192.168.56.107:6443 --kubeconfig=kube-proxy.kubeconfig

kubectl config set-credentials kube-proxy --client-certificate=/opt/kubernetes/ssl/kube-proxy.pem --client-key=/opt/kubernetes/ssl/kube-proxy-key.pem --embed-certs=true --kubeconfig=/opt/kubernetes/cfg/kube-proxy.kubeconfig

kubectl config set-context default --cluster=kubernetes --user=kube-proxy --kubeconfig=kube-proxy.kubeconfig

kubectl config use-context default --kubeconfig=/opt/kubernetes/cfg/kube-proxy.kubeconfig
```

3.4）创建服务配置文件（不同节点修改成不同ip）

```bash
cat > /opt/kubernetes/cfg/kube-proxy.yaml << "EOF"
apiVersion: kubeproxy.config.k8s.io/v1alpha1
bindAddress: 192.168.56.113
clientConnection:
  kubeconfig: /opt/kubernetes/cfg/kube-proxy.kubeconfig
clusterCIDR: 10.244.0.0/16
healthzBindAddress: 192.168.56.113:10256
kind: KubeProxyConfiguration
metricsBindAddress: 192.168.56.113:10249
mode: "ipvs"
EOF
```

3.5）创建服务启动文件

```bash
cat >  /opt/kubernetes/cfg/kube-proxy.service << "EOF"
[Unit]
Description=Kubernetes Kube-Proxy Server
Documentation=https://github.com/kubernetes/kubernetes
After=network.target

[Service]
WorkingDirectory=/var/lib/kube-proxy
ExecStart=/usr/local/bin/kube-proxy \
  --config=/opt/kubernetes/cfg/kube-proxy.yaml \
  --alsologtostderr=true \
  --logtostderr=false \
  --log-dir=/var/log/kubernetes \
  --v=2
Restart=on-failure
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF
```

3.6）同步文件到集群节点

```
cp kube-proxy*.pem /etc/kubernetes/ssl/
cp kube-proxy.kubeconfig kube-proxy.yaml /etc/kubernetes/
cp kube-proxy.service /usr/lib/systemd/system/
	
for i in k8s-master2 k8s-master3 k8s-worker1;do scp kube-proxy.kubeconfig kube-proxy.yaml $i:/etc/kubernetes/;done
for i in k8s-master2 k8s-master3 k8s-worker1;do scp  kube-proxy.service $i:/usr/lib/systemd/system/;done
```

注意：同步文件后需要根据节点ip修改kube-proxy.yaml文件

3.7)启动

```
systemctl daemon-reload && systemctl enable --now kube-proxy && systemctl status kube-proxy
```

##### 四.网络组件calico，CoreDNS部署及ngxin部署验证

```
一。网络组件calico，CoreDNS部署及ngxin部署验证
	
	1.calico安装部署
	wget https://docs.projectcalico.org/v3.19/manifests/calico.yaml
	修改文件
	3683             - name: CALICO_IPV4POOL_CIDR
	3684               value: "10.244.0.0/16"
	执行文件
	$ kubectl apply -f calico.yaml
	查看
	$ kubectl get pods -A
	
	2.CoreDNS安装部署
	$ wget https://github.com/kubernetes/kubernetes/blob/master/cluster/addons/dns/coredns/coredns.yaml.base
	$ cp coredns.yaml.base  coredns.yaml
	执行文件
	kubectl apply -f coredns.yaml
	
	3.部署验证
cat >  nginx.yaml  << "EOF"
---
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-web
spec:
  replicas: 2
  selector:
    name: nginx
  template:
    metadata:
      labels:
        name: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.19.6
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service-nodeport
spec:
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30001
      protocol: TCP
  type: NodePort
  selector:
    name: nginx
EOF
	
	执行文件：
	kubectl apply -f nginx.yaml
	验证：
	$ kubectl get pods -o wide
	$ kubectl get all
	
```



