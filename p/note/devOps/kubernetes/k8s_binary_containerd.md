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
cat > etcd-csr.json<< EOF
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
cat > etcd.conf << EOF
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
cat > etcd.service << EOF
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
$ ETCDCTL_API=3 /opt/etcd/etcdctl --write-out=table --cacert=/opt/ssl/ca.pem --cert=/opt/etcd/ssl/etcd.pem --key=/opt/etcd/ssl/etcd-key.pem --endpoints="https://192.168.56.112:2380,https://192.168.56.113:2380,https://192.168.56.114:2380" endpoint health

# 性能检查
$ ETCDCTL_API=3 /opt/etcd/etcdctl --write-out=table --cacert=/opt/ssl/ca.pem --cert=/opt/etcd/ssl/etcd.pem --key=/opt/etcd/ssl/etcd-key.pem --endpoints=https://192.168.56.112:2380,https://192.168.56.113:2380,https://192.168.56.114:2380 check perf
```





