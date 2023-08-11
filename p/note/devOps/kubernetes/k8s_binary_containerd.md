# kubernetes二进制高可用部署(Runtime Containerd)

##### kuernetes架构



##### 集群环境



##### 软件节点



##### 网络分配



##### 一。安装cfssl生成集群ca并安装etcd集群

###### 1.安装cfssl并生成集群证书

1.1）安装cfssl

```
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
```

1.3).创建需要的目录

```bash
$ mkdir /opt/kubernetes&&mkdir /opt/kubernetes/ssl&&mkdir /opt/kubernetes/logs&&mkdir /opt/kubernetes/cfg
```

###### 2.安装部署kube-apiserver

2.1）.创建kube-apiserver-csr.json请求文件

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

# 拷贝etcd/ssl信息（保证master节点都有etcd/ssl证书文件）
scp /opt/etcd/ssl root@node2:/opt/etcd/ssl
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
      "O": "system.masters",
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
//配置哪个集群,证书
$ kubectl config set-cluster kubernetes --certificate-authority=/opt/ssl/ca.pem --embed-certs=true --server=https://192.168.56.107:6443 --kubeconfig=kube.config
//证书角色管理员
$ kubectl config set-credentials admin --client-certificate=admin.pem --client-key=admin-key.pem --embed-certs=true --kubeconfig=/opt/kubernetes/cfg/kube.config
//设置安全上下文
$ kubectl config set-context kubernetes --cluster=kubernetes --user=admin --kubeconfig=kube.config
//设置安全上下文
$ kubectl config set-context kubernetes --kubeconfig=kube.config
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
//配置哪个集群,证书
$ kubectl config set-cluster kubernetes --certificate-authority=/opt/ssl/ca.pem --embed-certs=true --server=https://192.168.56.107:6443 --kubeconfig=kube-controller-manager.kubeconfig
//证书角色管理员
$ kubectl config set-credentials system:kube-controller-manager --client-certificate=/opt/kubernetes/ssl/kube-controller-manager.pem --client-key=/opt/kubernetes/ssl/kube-controller-manager-key.pem --embed-certs=true --kubeconfig=/opt/kubernetes/cfg/kube-controller-manager.kubeconfig
//设置安全上下文
$ kubectl config set-context system:kube-controller-manager --cluster=kubernetes --user=system:kube-controller-manager --kubeconfig=kube-controller-manager.kubeconfig
//设置安全上下文
$ kubectl config set-context system:kube-controller-manager --kubeconfig=kube-controller-manager.kubeconfig
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

