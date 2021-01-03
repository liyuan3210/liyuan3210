# kubernetes手动安装etcd,flannel

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

