# k8s核心
#### 测试集群命令：

kubectl get nodes	//查看节点

kubectl get ns		//查看命名空间

```
kubectl create deployment mynginx --image=nginx  -o yaml --dry-run > mynginx.yaml
kubectl expose deployment mynginx --port=80  --target-port=80 --type=NodePort -o yaml --dry-run > mynginx-service.yaml
kubectl get pod,svc
访问地址：
通过命令kubectl get pods -o wide查看在哪个节点,通过节点ip访问
通过命令kubectl get svc查看service指向端口
http://NodeIP:Port
```

kubectl工具:

```
kubectl语法格式：
	kubectl [command] [type] [name] [flags]
command:指定要对资源的操作．如create,get,describe,delete
type:指定资源类型，如 pods，pod， po
name:指定具体名称
flags:其它标识

查看kubectl帮助:
kubectl --help
查看具体命令帮助
kubectl create --help
```

### 一．yaml资源编排

１.YAML语法：

```
１．通过缩进表示层级关系
２．通过空格表示缩进，不能通过Tab缩进
３．通常开头是两个空格，`:`后面要跟一个空格，冒号，逗号等后面
4.一个`-`表示一个普通格式，｀---｀表示一个新的yaml文件
5.`#`表示注释

```

２．YAML文件组成部分,最简单YAML

yaml组成部分

```
控制器定义
    apiVersion:
    kind:
        ...
    spec:
    ....
被控制对象
	template:
		...
	spec:
		...
```

字段说明：

```
apiVersion:api版本			//kubectl api-versions查看
kind:资源类型					//kubectl api-resources查看
metadata:资源元数据
spec:资源规格
replicas:副本数量
selector:标签选择器
template:pod模板
metadata:pod元数据
spec:pod规格
containers:容器配置
```

快速编写yaml文件方法：

​		１>第一种 使用 kubectl create 命令生成yaml文件

```
kubectl create deployment web --image=nginx -o yaml --dry-run > web.yaml	//--dry-run 表示不在集群执行
```

​		２>第二种　使用kubectl get 命令导出yaml文件

```
kubectl get deploy nginx -o=yaml --export > nginx.yaml
```

### 二．pod介绍

###### 概念：

（１）pod是系统中可以创建的最小单元，一个pod可以包含多个容器

（２）没一个pod都有一个特殊的被称为＂跟容器＂的pause容器，pause容器对应的镜像属于k8s平台的一部分，

除了pause容器，每个pod还包含一个或多个紧密相关的用户业务容器

（３）一个pod中容器共享网络命名空间

（４）pod是短暂的

###### pod存在意义：

（１）容器创建docker,一个容器是一个应用程序，一个进程．docker是单线程

（２）pod是多进程设计，运行多个应用程序

（３）pod存在为了亲密性存在
两个应用之间进行交互，网络间调用，两个应用需要平凡调用

###### pod实现机制：

（１）共享网络

容器本身是隔离的（namespace，cgroup）

通过pause容器，把其他业务容器加入到pause容器里面，让所有业务容器在同一个名称空间中(ns)，可以实现网络共享

```
# 可以配置多个容器 -name
apiVersion:v1
kind:Pod
metadata:
  name:my-pod
 spec:
 	containers:
 	- name: write
 	......
 	- name: read
 	......
```

（２）共享存储

引入数据卷概念Volumn,使用数据卷进行持久化存储

```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
#挂载数据卷(写)
  - name: write
    image: centos
    command: ["bash","-c","for i in{1..100};do echo $i >> /data/hello;sleep 1;done"]
    volumeMounts:
    - name: data
      mountPath: /data

#挂载数据卷(读)
  - name: read
    image: centos
    command: ["bash","-c","tail -f /data/hello"]
    volumeMounts:
    - name: data
      mountPath: /data
#定义数据卷
  volumes:
  - name: data
    emptyDir: {}
```

###### 基本策略：

（１）镜像拉取策略

```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.14
    imagePullPolicy: Always

# 策略　imagePullPolicy
IfNotPresent: 默认值，　镜像不在主机上才进行拉取
Ａlways: 每次创建Pod都会重新拉取一次镜像
Ｎever: pod永远不会主动拉取这个镜像

```

（２）资源限制

根据配置的requests，limits规则来指定pod创建到哪个机器节点上

```
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: db
    image: mysql
    env:
    - name: MYSQL_ROOT_PASSWORD
      value: "password"
# 调度配置
     resources:
       requests:
         memory: "64Mi"
         cpu: "250m"
# 没有最小限制，这里表示最大限制         
        limits:					
          memory: "128Mi"
          cpu: "500m"
    
```

（３）重启策略

```
apiVersion: v1
kind: Pod
metadata:
  name: dns-test
spec:
  containers:
  - name: busybox
    image: busybox:1.28.4
    args:
    - /bin/sh
    - -c
    - sleep 3600
   restartPolicy: Never

# 策略　restartPolicy
Ａlways: 默认值，　容器终止退出,总是重启容器
OnFailure: 当容器异常退出(退出状态码非０)时，才重启容器
Ｎever: 容器终止退出，从不重启容器
```

（４）健康检查

kubectl get pods查看容器状态为running状态表示正常，但是在运行java中容器运行正常，但是出现java堆内存溢出，就不能对外提供服务了．

所以通过容器检查就不能准确判断应用运行是否正常了．

```
应用层面健康检查
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy
   livenessProbe:
     exec:
       command:
       - cat
       - /tmp/healthy
       initialDelaySeconds: 5
       periodSeconds: 5
       
两种检查机制：
１．livenessProbe(存活检查)
如果检查失败，将杀死容器，根据pod的restartPolicy来操作
２．readinessProbe（就绪检查）
如果检查失败，k8s会把pod从service endpoints中剔除

probe支持三种检查方法：
1.httpGet
发送http请求，返回２００－４００范围码为成功
2.exec
执行shell命令返回状态码是０为成功
３．tcpSocket
发起tcp　socket建立成功

linux命令查看状态码
echo $?	//查看上一个linux命令执行结果状态码，如果是０表示成功，如果是１表示失败
```

###### pod调度策略

（１）创建pod流程

首先用户执行 create pod ->API server（调用master接口） ->etcd(存储)

scheduler（监控）->API server->etcd读取－调度算法调到某个节点上

```
step.1
kubectl 向 k8s api server 发起一个create pod 请求(即我们使用Kubectl敲一个create pod命令) 。
step.2
k8s api server接收到pod创建请求后，不会去直接创建pod；而是生成一个包含创建信息的yaml。
step.3
apiserver 将刚才的yaml信息写入etcd数据库。到此为止仅仅是在etcd中添加了一条记录， 还没有任何的实质性进展。
step.4
scheduler 查看 k8s api ，类似于通知机制。
首先判断：pod.spec.Node == null?
若为null，表示这个Pod请求是新来的，需要创建；因此先进行调度计算，找到最“闲”的node。
然后将信息在etcd数据库中更新分配结果：pod.spec.Node = nodeA (设置一个具体的节点)
ps:同样上述操作的各种信息也要写到etcd数据库中中。
step.5
kubelet 通过监测etcd数据库(即不停地看etcd中的记录)，发现 k8s api server 中有了个新的Node；
如果这条记录中的Node与自己的编号相同(即这个Pod由scheduler分配给自己了)；
则调用node中的docker api，创建container。
 
kubernetes 创建 ReplicaSet 的 工作流：
???
```

（２）影响调度情况(以下三种)

１．pod资源限制对pod调度产生影响

２．节点选择器标签nodeSelector

首先要对节点打标签,kubectl label node node1 env_role=dev

查看,kubectl get nodes node1 --show-labels

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-example
spec:
  nodeSelector:
    env_role: dev
  containers:
  - name: nginx
    image: nginx:1.15
```

3.节点亲和性

```
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
  # 硬亲和性(约束条件必须满足)
    requiredDuringSchedulingIgnoredDuringExecution::
      nodeSelectorTerms:
      - matchExpressions:
        - key: env_role
          operator: In
          values:
          - dev
          - test
    ＃　软亲和性（尝试满足，不保证满足）软亲和性（尝试满足，不保证满足）
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 1
      preference:
        matchExpressions:
        - key: group
          operator: In
          values:
          - otherprod
  containers:
  - name: webdemo
    image: nginx
    
# 节点亲和性　nodeAffinity和前面nodeSelector基本一样的，根据节点上标签约束来决定pod调度到哪些节点上

＃两类亲和性
１．硬亲和性(约束条件必须满足)
２．软亲和性（尝试满足，不保证满足）
支持操作符(operator: In):In,NotIn,Exists,Gt,Lt,DoesNotExists
```

４．污点和污点容忍

nodeSelector和nodeAffinity：pod调度到某些节点上，pod属性，调度时候实现

Taint污点：节点不做普通分配调度，是节点属性

应用场景：

```
１．专用节点
２．配置特点硬件节点
３．基于Taint驱逐
```

查看污点:

```
$ kubectl describe node node1 | grep Taint
    污点值有：
    *NoSchedule:一定不被调度
    *PreferNoSchdule:尽量不被调度
    *NoExecute:不会调度，并且还会驱逐Node已有pod
```

为节点添加污点: 

kubectl taint node [node] key=value:污点三个值

```
打污点前
$ kubectl create deployment web --image=nginx	//创建一个pod
$ kubectl scale deployment web --replicas=5		//扩５个
$ kubectl get pods -o wide	//查看pod分配节点情况,平均分配
删除pod
$ kubectl delete deployment web

1.打污点:
$ kubectl taint node node1 env_role=yes:NoSchedule	//添加污点
$ kubectl describe node node1 | grep Taint	//查看
打污点前后验证
$ kubectl create deployment web --image=nginx	//创建一个pod
$ kubectl scale deployment web --replicas=5		//扩５个
$ kubectl get pods -o wide	//查看pod分配节点情况,平均分配

2.删除污点：
kubectl taint node node1 env_role:NoSchedule-

3.污点容忍（即便接是NoSchedule，也可以被调度）：
spec:
  tolerations:
  - key: "key"			//值env_role
    operator: "Equal"
    value: "value"		//值yes
    effect: "NoSchedule"
  containers:
  - name: webdemo
    image: nginx

```

### 三．controller介绍

什么是controller:

```
在集群上管理和运行容器的对象
*确保预期的pod副本数量

*无状态应用部署
*有状态应用部署

*确保所有node运行同一个pod

*一次性和定时任务



Pod和Controller关系:
	需要在pod上打label标签，控制器上也要打上相同标签(selector)
	pod和controller之间通过label标签建立关系,pod通过controller实现应用的运维，比如伸缩．滚动升级等等
```

常见控制器deployment应用场景：

```
*部署无状态应用
*管理pod和replicaSet
*部署，滚动升级等功能
应用场景:web服务,微服务
```

（1）deployment部署无状态应用：

```
# 生成文件
$ kubectl create deployment web --image=nginx --dry-run -o yaml > web.yaml
# 根据yaml部署应用
$ kubectl apply -f web.yaml
# 查看
$ kubectl get pods -o wide
# 对外发布（暴露对外端口号）
$ kubectl expose deployment web --port=80 --type=NodePort --target-port=80 --name=web1  -o yaml > web1.yaml
# 根据yaml部署应用
$ kubectl apply -f web1.yaml
# 查看状态及对外访问端口号
$ kubectl get pods,svc -o wide

#通过节点访问
http://NodePort:port
```

（2）升级回滚，弹性伸缩

升级回滚：

```
# 首先基于上面web.yaml修改nginx镜像版本为1.14,还有副本数
# kubectl create deployment web --image=nginx --dry-run -o yaml > web.yaml
$ kubectl apply -f web.yaml
# 查看启动状态
$ kubectl get pods -o wide
# 通过命令docker images查看其它节点nginx镜像版本
$ docker images

# 1.版本升级
# nginx 升级版本
$ kubectl set image deployment web nginx=nginx:1.15
# 查看升级状态
$ kubectl rollout status deployment web

# 2.版本回退
# 查看历史版本
$ kubectl rollout history deployment web
# 版本回退两种方式
$ kubectl rollout undo deployment web //回退上一版本
$ kubectl rollout undo deployment web --to-revision=2 //回退指定版本,根据history查看revison指定
```

弹性伸缩：

```
$ kubectl scale deployment web --replicas=10

```

（3）部署有状态应用

无状态特点：

```
*认为每个pod都一样的
*没有顺序要求
*不用考虑在哪个node运行
*随意进行伸缩和扩展
```

有状态特点：

```
*上面因素都需要考虑到
*让每个pod独立的，保持pod启动顺序和唯一性
*唯一的网络标识符，持久存储
*有序，比如mysql
```

应用部署：

无头service，ClusterIp:none,

SatefulSet部署有状态

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: nginx-statefulset
  namespace: default
spec:
  serviceName: nginx
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        prots:
        - containerPort: 80
        
#执行：
$ kubectl apply -f sts.yaml

# 查看pod
$ kubectl get pods
# 使用唯一名称
nginx-statefulset-0
nginx-statefulset-1
nginx-statefulset-2

# 查看service
$ kubectl get svc	//CLUSTER-IP值为None

```

deployment和statefueset区别:有身份的(唯一标识)

* 根据主机名+按照一定规则生成域名
* 每个pod有唯一主机名
* 唯一域名：
  * 格式：主机名称 . service名称 . 名称空间 . svc . cluster . local
  * 实例：nginx-statefulset-0.nginx.default.svc.cluster .local



（4）DaemonSet,确保所有node运行同一个pod（守护进程）

场景：在每个node上运行一个Pod,新加入的node也同样运行在一个pod里面

实例：在每个node节点安装数据采集工具

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: ds-test
  labels:
    app: filebeat
spec:
  selector:
    matchLabels:
      app: filebeat
  template:
    metadata:
      labels:
        app: filebeat
    spec:
      containers:
      - name: logs
        image: nginx
        ports:
        - containerPort: 80
        valueMounts:
        - name varlog
          mountPath: /tmp/log
      volumes:
      - name: varlog
        hostPath:
          path: /var/log

# 部署前需要删掉
$ kubectl delete statefulset --all	//删掉statefulset
$ kubectl delete svc nginx	//删除service
# 部署
$ kubectl apply -f ds.yaml
# 查看
$ kubectl get pods
# 进入到某一个pod里面查看
$ kubectl exec -it ds-test-cbk6v bash
```

（5）一次任务和定时任务

一次任务job：

```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl","-Mbignum=bpi","-wle","print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4	//失败后重启4次,默认是6次

# 1.上面文件保存job.yaml,发布
$ kubectl create -f job.yaml
# 2.查看,完成一次status为completed
$ kubectl get pods -o wide

# 3.查看job
$ kubectl get jobs

# 4.查看结果
$ kubectl logs pi-qpqff
# 5.删除job
$ kubectl delete -f job.jaml
```

定时任务cronjob：

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  template:
    spec:
      containers:
      - name: hello
        image: busybox
        args:
        - /bin/sh
        - -c
        - date; echo Hello from the Kubernetes cluster   
      restartPolicy: OnFailure
      
# 1.保存上面文件为cronjob.yaml
$ kubectl apply -f conjob.yaml
# 2.查看,每执行一次多一个hello-1258374537-wkn79，completed
$ kubectl get pods -o wide
# 3.查看job执行情况
$ kubectl get cronjobs
# 4.查看结果
$ kubectl logs hello-1258374537-wkn79
# 5.删除？？？

```



### 四.srvice介绍 

定义一组pod访问规则：

```
service解决的问题
1.防止pod失联，因为pod的ip是不断变化的（服务发现）
2.定义一组pod访问策略(负载均衡)

pod和service关系
根据label和selector建立关系的

service对外有一个ip叫vip

```

常见service类型：

```
查看service类型：
$ kubectl expose --help
常见类型有：
ClusterIP(Default)：集群内部使用
NodePort：对外暴露访问时候
LoadBalancer：对外访问应用使用，公有云
```

service演示：

```
# 首先基于上面web.yaml，发布应用，kubectl get pods查看
# kubectl create deployment web --image=nginx --dry-run -o yaml > web.yaml
$ kubectl apply -f web.yaml
# 基于pod上面导出service
$ kubectl expose deployment web --port=80 --target-port=80 --dry-run -o yaml > service1.yaml

# 查看service（创建默认ClusterIP类型，集群内部可以访问）
$ kubectl get svc

# 修改上面service1.yaml文件
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web1	//名字改一下
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: web
  type: NodePort	//添加的
status:
  loadBalancer: {}

# 执行发布
$ kubectl apply -f service1.yaml

# 查看,通过任何节点及端口可以访问
$ kubectl get svc
```

LoadBalancer功能更加强大？？？



### 五．配置管理secret,configMap

**secret:**

作用：加密数据存在etcd里面，让pod容器以挂载volume方式进行访问

场景：凭证

base64编码linux脚本：echo -n 'admin' | base64

```
# 创建secret加密
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
  
# 以上面内容创建secret.yaml
$ kubectl create -f secret.yaml
# 查看
$ kubectl get secret
```

两种方式使用:

1）.以变量形式挂载到容器中

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx
    image: nginx
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password

# 上面内容，创建文件secret-var.yaml
$ kubect apply -f secret-var.yaml
# 查看pod
$ kubectl get pods
# 进入pod,并验证
$ kubectl exec -it mypod bash
$ echo $SECRET_USERNAME			//进入pod执行
$ echo $SECRET_PASSWORD			//进入pod执行
```

2）.以volume方式进行访问

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret

# 如果用变量形式挂载，需要先删除mypod
$ kubect delete -f secret-val.yaml
# 根据上面内容创建 secret-vl.yaml,并创建发布
$ kubect apply -f secret-vl.yaml
# 查看pod
$ kubectl get pods
# 进入pod,并验证
$ kubectl exec -it mypod bash
# 进入容器后查看/etc/foo
$ cat password
$ cat username
```

**ConfigMap:**

跟secret一样，但是不是加密的数据

场景：配置文件

可以先删掉secret与mypod

$ kubectl delete secret --all	//这是都删掉

$ kubectl delete Pod --all	//这是都删掉

```
# 新建redis.properties配置文件
redis.host=127.0.0.1
redis.port=6379
redis.password=123456

# 创建configMap
$ kubectl create configmap redis-config --from-file=redis.properties
# 查看
$ kubectl get cm
# 查看详细
$ kubectl describe cm redis-config
```

两种方式使用：

1）volume方式挂载到容器

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: busybox
      image: busybox
      command: ["/bin/sh","-c","cat /etc/config/redis.properties"]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: redis-config
  restartPolicy: Never
  
# 上面内容创建cm.yaml文件
$ kubectl apply -f cm.yaml
# 查看pod
$ kubectl get pods
# 查看日志
$ kubectl logs mypod
```

2）以变量形式挂载到pod容器中

做了上面首先要删除

kubectl delete -f cm.yaml

步骤：

​		1>创建yaml,声明变量信息 configmap创建
​		2>以变量挂载

声明变量信息：

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfig
  namespace: default
data:
  special.level: info
  special.type: hello

# 上面内容创建myconfig.yaml文件
$ kubectl apply -f myconfig.yaml
# 查看pod
$ kubectl get cm
```
容器验证
```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: busybox
      image: busybox
      command: ["/bin/sh","-c","echo $(LEVEL) $(TYPE)"]
      env:
        - name: LEVEL
          valueFrom:
            configMapKeyRef:
              name: myconfig
              key: special.level
        - name: TYPE
          valueFrom:
            configMapKeyRef:
              name: myconfig
              key: special.type
  restartPolicy: Never
  
# 上面文件保存为config-var.yaml并发布
$ kubectl apply -f config-var.yaml
# 查看
$ kubectl get pods
# 查看cm
$ kubectl get cm
# 通过日志查看
$ kubectl logs mypod
```



### 六．集群安全机制RBAC

认证 -> 鉴权(授权) -> 准入控制

进行访问时候，需要经过apiserver做统一协调

RBAC鉴权:

```
规划(资源[pod,node...],权限[create,get...])
↑
↑
↑
角色（Role ClusterRole）<---[角色绑定]--->主体(user group,serviceaccount)

*角色：
role:特定命名空间访问权限
clusterRole:所有命名空间访问权限

*角色绑定：
roleBinding:角色绑定到主体
ClusterRoleBinding:集群角色绑定到主体

*主体
user:用户
group:用户组
serviceAccount:服务账号

```

实际操作：

```
1.创建命名空间
$ kubectl create ns roledemo
$ kubectl get ns	//查看命名空间

2.在创建的命名空间创建pod
$ kubectl run nginx --image=nginx -n roledemo

3.创建角色
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: roledemo
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get","watch","list"]
  
# 上面内容创建rbac-role.yaml
$ kubectl apply -f rbac-role.yaml
# 查看roledemo命名空间角色
$ kubectl get role -n roledemo

4.创建角色绑定
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: roledemo
  name: read-pods
subjects:
- kind: User
  name: mary # Name is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role # this must be Role or ClusterRole
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
  
# 上面内容创建rbac-rolebinding.yaml
$ kubectl apply -f rbac-rolebinding.yaml
# 查看
$ kubectl get role,rolebinding -n roledemo

5.使用证书识别身份
视频里面有个脚本rabc-user.sh,需要ca-config.json,ca.csr,ca-csr.json,ca-key.pem，ca.pem。
执行后会生成一个mary-kubeconfig.
$ kubectl get pods -n roledemo	//可以查看
$ kubectl get svc -n roledemo	//报No resources found in roledemo namespace

建议在二进制搭建集群后再操作
```

### 七．Ingress

NodePort缺陷：

1.每个节点上都会起到端口，访问时候通过节点ip+端口访问

2.一个端口使用一次，一个端口对应一个应用

3.实际访问中都是使用域名，根据不同域名转到不同端口服务中



Ingress流程：

​			【Ingress入口】

域名1	，						域名2

​	↓										↓

service							service

​	↓										↓

pod1,pod2					pod3,pod4



Ingress和Pod关联：

*pod和Ingress通过service关联

*Ingress作为统一入口，由srevice关联一组pod



使用Ingress:

第一步：部署Ingress controller

​			选择官方维护nginx控制器，实现部署

​			1）创建nginx应用，暴露端口NodePort

​			$ kubectl create deployment web --image=nginx

​			$  kubectl get pods	//查看

​			2）创建service

​			$ kubectl expose deployment  web --port=80 --target-port=80 --type=NodePort

​			$ kubectl get svc

第二步：部署Ingress controlle

​			下载Ingress控制器yaml文件，有个地方需要注意HostNetwork修改为true

```
表示对外暴露host网络
...
kind: Deployment		//191行
...
spec:
...
  template:
	...
    spec:
      hostNetWork: true		//修改此处
```

​			# 上面文件命名为ingress-conyaml

​			$ kubectl apply -f ingress-conyaml

​			# 查看Ingress状态

​			$ kubectl get pods -n ingress-nginx（-n 后面跟名称控制）

第三步：创建Ingress规则

```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: example.ingressdemo.com
    http:
      paths:
      - path: /
        backend:
          serviceName: web
          servicePort: 80
# 上面文件创建ingress-http.yaml,创建一下
$ kubectl apply -f ingress-http.yaml
# 查看ingress-nginx在哪个节点上，可以在节点上netstat -lntp查看监听端口
$ kubectl get pods -n ingress-nginx -o wide
```

最后配置宿主机hosts文件，上面查到的ip指向定义的域名即可

192.168.44.145  example.ingressdemo.com

最后宿主机上访问：example.ingressdemo.com

### 八．Helm

官网：

https://helm.sh/



Helm类似linux下的yum/apt，是k8s下的包管理工具



Helm三个重要概念:

1>helm：是一个命令行工具

2>chart：把yaml进行打包，是yaml一个集合

3>relesase：基于chart部署的实体，应用级别版本管理



Helm版本比较：

v2版本(老版本)，v3版本(新版本)，

1> v3版本删除Tiller

2>release可以在不同命名空间重用

3>将chart推送到docker仓库中

V3版本架构：

Helm chart	--->   kube-config   --->  kube-apiserver  --->  [deployment，service，ingress]



1.Helm安装：

下载压缩文件，直接解压到linux下的/usr/bin目录下面即可

```
//添加仓库
$ helm repo add 仓库名称[自定义] https://charts.helm.sh/stable
//查看仓库
$ helm repo list
//更新源
$ helm repo update
//删除源
$ helm repo remove 仓库名称[自定义]
```

2.快速使用

```
# 搜索应用
$ helm search repo xxx/weave
# 根据搜索内容进行安装
$ helm install ui xxx/weave
# 查看安装之后状态
$ helm list
$ helm status ui
# 查看svc
$ kubectl get svc	//ui-weave-scope
$ kubect edit svc ui	//编辑service的yaml文件,对外暴露端口,把ClusterIp改为NodePort
```

3.自定义chart部署

```
#使用名称创建chart，基础模板
$ helm create mychart

charts	//空文件夹
Chart.yaml	//当前chart属性配置信息
templates	//自己写的文件放在templates里面,删掉里面文件
values.yaml	//yaml文件可以使用的局变量

# 在templates文件夹创建两个yaml文件，先删除里面文件rm -rf *
# 生成deployment.yaml
$ kubectl create deployment web1 --image=nginx --dry-run -o yaml > deployment.yaml

# 生成service.yaml需要先创建上面控制器web1,然后才能执行下面的生成语句，（为了验证可以先生成service.yaml后在执行kubectl delete deployment web1删除）
$ kubectl expose deployment web1 --port=80 --target-port=80 --type=NodePort --dry-run -o yaml > service.yaml

# 开始安装mychart
$ helm install web1 mychart

# 应用升级(修改后)
$ helm upgrade web1 mychart
```

4.动态传参使用模板

values.yaml（配置全局变量）	------>		在templates具体自定义文件中取值

把变的值提取出来放在values.yaml里面。



在values.yaml中定义值,大体有这几个地方不同的,可以自定义值的：

```
image: nginx
tag: 1.16
label: nginx
port: 80
replicas: 1
```



在templates的yaml文件中使用values.yaml

```
通过表达式使用全局变量：
* {{ .Values.变量名称}}
* {{ .Release.Name}}	//动态名称
例如：
	apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: {{ .Release.Name}}-deply
    spec:
      containers:
      - name: nginx
        image: {{ .Values.image}}
     ......

# 修改完后可以尝试运行，没问题就打印yaml内容
$ helm install --dry-run web2 mychart
# 没问题就运行
$ helm install web2 mychart
```



### 九．持久化存储

**1.nfs存储：**

首先找一台服务器，安装nfs，设置挂载路径并启动服务

```
1>centos安装: 
$ yum install -y nfs-utils

2>设置挂载路径(没有需要创建nfs目录)vi /etc/exports：
/data/nfs *(rw.no_root_squash)

3>启动服务（nfs服务端）
$  systemctl start nfs

4>需要在k8s节点也安装nfs
$ yum install -y nfs-utils
```

在k8s集群部署，使用nfs持久存储

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-dep1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: wwwroot
          mountPath: /usr/share/nginx/html
        ports:
        - containerPort: 80
      volumes:
        - name: wwwroot
          nfs:
            server: 192.168.44.134
            path: /data/nfs

# 上面文件生成nfs-nginx.yaml,并发布
$ kubectl apply -f nfs-nginx.yaml
# 查看日志
$ kubectl get pods
$ kubectl describe pod nginx-dep1-231341234-2342
# 进入pod查看文件目录/usr/share/nginx/html
$ kubectl exec -it nginx-dep1-231341234-2342 bash

# 进入nfs服务器，在/data/nfs目录里面创建一个index.html文件
# 再进入pod 查看文件目录/usr/share/nginx/html
$ kubectl exec -it nginx-dep1-231341234-2342 bash

# 对外暴露端口,在浏览器查看
$ kubectl expose deployment nginx-dep1 --port=80 --target-port=80 --type=NodePort
```

**2.pv和pvc:**

PV(持久化存储，对存储资源进行抽象，对外提供可以调用的地方，叫生产者)

PVC(用户调用，不需要关心内部实现细节,叫消费者)

实现流程：

1.应用/容器部署(通过pvc绑定pv)

2.定义pvc（绑定pv）

3.定义pv(数据存储服务器ip，路径，专业人员做的),



pv与pvc匹配模式

1.容量(如50G)，2.权限(如读写)



实例:

首先删除nfs-nginx，$ kubectl delete -f nfs-nginx.yaml

```
作用：
	1.应用/容器部署
	2.定义pvc

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-dep1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: wwwroot
          mountPath: /usr/share/nginx/html
        ports:
        - containerPort: 80
      volumes:
        - name: wwwroot
          persistentVolumeClaim:
            claimName: my-pvc
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
      
# 上面内容保存pvc.yaml文件,执行部署
$ kubectl apply -f pvc.yaml
```

定义pv：

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  nfs:
    path: /data/nfs
    server: 192.168.44.134
    
# 上面内容保存pv.yaml文件,执行部署
$ kubectl apply -f pv.yaml
```

查看pv,pvc：

$ kubectl get pv,pvc

进入容器查看验证：

$ kubectl get pods	//查看pod

$ kubectl exec -it nginx-dep1-231341234-2342 bash	//查看/usr/share/nginx/html目录

### 十.dashboard安装

官方
	https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
	https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
	https://github.com/kubernetes/dashboard

安装：

```
//下面地址可能不能访问，需要根据类似Flannel安装方式解决，先把文件下载下来然后执行发布命令
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.1.0/aio/deploy/recommended.yaml
//里面有两个镜主要的镜像，需要查看本网络是否能获取，否则要从镜像托管
kubernetesui/dashboard:v2.1.0
kubernetesui/metrics-scraper:v1.0.6
//默认Secret是不能用用的，需要把recommended.yaml注释掉，如下
    #apiVersion: v1
    #kind: Secret
    #metadata:
    #  labels:
    #    k8s-app: kubernetes-dashboard
    #  name: kubernetes-dashboard-certs
    #  namespace: kubernetes-dashboard
    #type: Opaque
//默认type是ClusterIP对外不能访问需要改成NodePort
    kind: Service
    apiVersion: v1
    metadata:
      labels:
        k8s-app: kubernetes-dashboard
      name: kubernetes-dashboard
      namespace: kubernetes-dashboard
    spec:
      type: NodePort	//添加到这里
      ports:
        - port: 443
          targetPort: 8443
      selector:
        k8s-app: kubernetes-dashboard
        
//执行发布
$ kubectl apply -f recommended.yaml

//创建签名
#创建key目录并进入
mkdir key && cd key
#生成证书
openssl genrsa -out dashboard.key 2048
openssl req -new -out dashboard.csr -key dashboard.key -subj '/CN=kubernetes-dashboard-certs'
openssl x509 -req -in dashboard.csr -signkey dashboard.key -out dashboard.crt
#删除原有的证书secret，v2.0是 -n kubernetes-dashboard
kubectl delete secret kubernetes-dashboard-certs -n kubernetes-dashboard
#删除原有的证书pod，v2.0是 -n kubernetes-dashboard
kubectl delete pod kubernetes-dashboard -n kubernetes-dashboard
#创建新的证书secret
kubectl create secret generic kubernetes-dashboard-certs --from-file=dashboard.key --from-file=dashboard.crt -n kubernetes-dashboard
#查看dashboard pod，v2.0是 -n kubernetes-dashboard
kubectl get pod -n kubernetes-dashboard

# 查看对外访问端口
kubectl get svc -n kubernetes-dashboard -o wide
```

  产生token访问：

$ kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep dasboard-admin | awk '{print $1}')

