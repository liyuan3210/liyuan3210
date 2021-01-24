# kubeadm安装kubernetes

```
kubeadm集群安装：
	操作系统：centos7
	docker：docker-ce-18.06.1.ce-3.el7
	k8s:kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
	
没有的：
	1.将桥接的IPV4流量传递到iptables的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system # 生效

	2.只在master添加hosts
	ip master
	ip node1
	......
	
	3.时间同步
	yum install ntpdate -y
	ntpdate time.windows.com	//同步时间
	0 12 * * * * /usr/sbin/ntpdate 192.168.0.1	//定时器同步
	集群时间同步方案
	https://www.cnblogs.com/liushui-sky/p/9203657.html
	
	4.flannel安装问题
	https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
	下载不了解决办法：
        1.先到云实例机器上ping域名raw.githubusercontent.com,得到151.101.228.133
        2.然后配置/etc/hosts域名指向151.101.228.133 raw.githubusercontent.
        3.配置好后wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### kubeadm安装（第三版）

```
节点介绍:
	master
	node1
	node2
版本介绍：
	操作系统：centos7
	docker：docker-ce-18.06.1.ce-3.el7
	kubernetes：kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
一.环境初始化
	1>关闭防火墙(每个节点操作)
		systemctl status firewalld.service	//查看防火墙状态
		systemctl stop firewalld.service	//停止防火墙
		systemctl disable firewalld.service	//禁止防火墙开机启动
	2>关闭swap(每个节点操作)
		swapoff -a	//临时关闭
		永久关闭,把vi /etc/fstab文件中带有swap的行注释。
		#/dev/mapper/centos-swap swap                    swap    defaults        0 0
	3>关闭SELinux(每个节点操作)
		setenforce 0	//临时关闭(设置SELinux 成为permissive模式 临时关闭selinux的)
		vi /etc/sysconfig/selinux	//永久关闭,修改SELINUX属性为disabled
		SELINUX=disabled
	4>在master设置hosts配置(只配置master节点)
        cat >> /etc/hosts << EOF
        192.168.122.242 master
        192.168.122.84 node1
        192.168.122.177 node2
        EOF
	5>设置主机名称（在命名的各个节点执行）
		hostnamectl set-hostname master
		hostnamectl set-hostname node1
		hostnamectl set-hostname node2
	6>将桥接的IPV4流量传递到iptables的链(每个节点执行)
        $ cat > /etc/sysctl.d/k8s.conf << EOF
        net.bridge.bridge-nf-call-ip6tables = 1
        net.bridge.bridge-nf-call-iptables = 1
        EOF
        $ sysctl --system
    7>时间同步(每个节点执行)
        yum install ntpdate -y
        ntpdate time.windows.com	//同步时间
        0 12 * * * * /usr/sbin/ntpdate 192.168.0.1	//定时器同步
        集群时间同步方案
        https://www.cnblogs.com/liushui-sky/p/9203657.html
二.安装docker,kubernetes,kubeadm
	1>安装docker(三节点都安装)
        # step 1: 安装必要的一些系统工具
        $ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
        # Step 2: 添加软件源信息
        $ sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
        # 查看版本
        $ yum list docker-ce --showduplicates | sort -r
        # 安装
        $ yum -y install docker-ce-18.06.1.ce-3.el7
     
	docker服务自启,启动
			systemctl enable docker
			systemctl start docker
			systemctl restart docker
	配置仓库源(配置好后要重启docker)：
       vi /etc/docker/daemon.json
        {
          "registry-mirrors": ["https://6brt8p5b.mirror.aliyuncs.com"]
        }
	2>安装kubelet(三节点都安装)
	配置源
    cat <<EOF > /etc/yum.repos.d/kubernetes.repo
    [kubernetes]
    name=Kubernetes
    baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
    enabled=1
    gpgcheck=1
    repo_gpgcheck=1
    gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
    EOF

    #setenforce 0
    yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
    systemctl enable kubelet
    #systemctl enable kubelet && systemctl start kubelet
    
    3>部署master节点
    kubeadm init --image-repository registry.aliyuncs.com/google_containers --apiserver-advertise-address=192.168.122.242 --kubernetes-version=v1.18.0 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12
    
    获取加入节点token（下面ip写成master节点ip）
    kubeadm join 192.168.122.242:6443 --token 0h0pa9.zhzc198p8zavc09c \
    --discovery-token-ca-cert-hash sha256:d2f05949d9f62bddee3171e48fd895653f6fd7f0092718318975fc2dbef0e5e1
    
    主节点执行
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    
    4>安装Flannel(quay.io/coreos/flannel镜像要每台机器单独导入)
    项目地址:https://github.com/coreos/flannel
    https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
	下载不了解决办法：
        1.先到云实例机器上ping域名raw.githubusercontent.com,得到151.101.228.133
        2.然后配置/etc/hosts域名指向151.101.228.133 raw.githubusercontent.
        3.配置好后wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
        
         修改kube-flannel.yml文件:
                        如果是使用自己阿里docker镜像源,kube-flannel.yml下载下来后应该配置如下两项:
                    apiVersion: apps/v1
                        kind: DaemonSet
                        metadata:
                          name: kube-flannel-ds-amd64
                        ....
                        spec:
                              initContainers:
                              - name: install-cni
                                image: registry.cn-shanghai.aliyuncs.com/kubernetes-3210/flannel:v0.12.0-amd64	//前面修改为registry.cn-shanghai.aliyuncs.com/kubernetes-3210
                                ...
                              containers:
                              - name: kube-flannel
                                image: registry.cn-shanghai.aliyuncs.com/kubernetes-3210/flannel:v0.12.0-amd64	//前面修改为registry.cn-shanghai.aliyuncs.com/kubernetes-3210
                                
     镜像quay.io/coreos/flannel很难下载问题
     	托管在阿里云里面
							
    5>部署node节点
     kubeadm join 192.168.122.242:6443 --token 0h0pa9.zhzc198p8zavc09c \
    --discovery-token-ca-cert-hash sha256:d2f05949d9f62bddee3171e48fd895653f6fd7f0092718318975fc2dbef
				
	6>部署验证
		kubectl get nodes
		
```



### 一.kubeadm安装（二版）

```
一.环境准备
	master
	node1
	node2
	必须环境：
	1>.配置好host与免密登录ssh(参考hadoop)
	2>.注意每个节点时间同步问题？？？
	3>.关闭防火墙？？？
		systemctl status firewalld.service	//查看防火墙状态
		systemctl stop firewalld.service	//停止防火墙
		systemctl disable firewalld.service	//禁止防火墙开机启动
	4>.关闭swap？？？
		swapoff -a	//临时关闭
		永久关闭,把/etc/fstab文件中带有swap的行注释。
		#/dev/mapper/centos-swap swap                    swap    defaults        0 0
	5>.关闭SELinux？？？
		setenforce 0	//临时关闭(设置SELinux 成为permissive模式 临时关闭selinux的)
		vi /etc/sysconfig/selinux	//永久关闭,修改SELINUX属性为disabled
		SELINUX=disabled
	6>.设置主机名称
		hostnamectl set-hostname master
		hostnamectl set-hostname node1
		hostnamectl set-hostname node2
二.安装主节点
	阿里云yum镜像源：
		https://developer.aliyun.com/mirror/
	腾讯云镜像源：
		https://mirrors.cloud.tencent.com/
	1.安装docker
		yum install -y yum-utils device-mapper-persistent-data lvm2	//用到就装
		yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
		yum install -y https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/edge/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
		yum install -y docker-ce-19.03.9 docker-ce-cli-19.03.9
		docker服务自启,启动
			systemctl enable docker
			systemctl start docker
		docker验证
			docker pull hello-world
			docker run hello-world
			
		注意：
		根据官方文档可以看到最新release版本19.03.9,(安装我们也安装最新的)
		https://docs.docker.com/engine/release-notes/
			
	2.安装kubeadm
配置源
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

#setenforce 0
yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
systemctl enable kubelet
#systemctl enable kubelet && systemctl start kubelet
		
		注意:
		根据官方文档可以看到最新release版本1.18.0 ,(安装我们也安装最新的)
		https://kubernetes.io/docs/setup/release/notes/#server-binaries
		
	3.主节点初始化
		1>用到了阿里云docker镜像registry.aliyuncs.com/google_containers
		kubeadm init --image-repository registry.aliyuncs.com/google_containers --apiserver-advertise-address=192.168.122.107 --kubernetes-version=v1.18.0 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12

		2>返回信息
			Your Kubernetes control-plane has initialized successfully!

			To start using your cluster, you need to run the following as a regular user:

			  mkdir -p $HOME/.kube
			  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
			  sudo chown $(id -u):$(id -g) $HOME/.kube/config

			You should now deploy a pod network to the cluster.
			Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
			  https://kubernetes.io/docs/concepts/cluster-administration/addons/

			Then you can join any number of worker nodes by running the following on each as root:

			kubeadm join 192.168.122.107:6443 --token amj4dy.cw4bwztapgbfy9n3 \
				--discovery-token-ca-cert-hash sha256:28e9ba2b6495373ab16299ad48c4db70a42857a984561097bc3ad5eef6328102
			
		3>根据提示在主节点root用户执行如下命令
			mkdir -p $HOME/.kube
			sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
			sudo chown $(id -u):$(id -g) $HOME/.kube/config
			
		4>.安装Flannel
			项目地址:https://github.com/coreos/flannel
				kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml		//最新v0.11.0
				kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/v0.12.0/Documentation/kube-flannel.yml	//指定版本
				kubectl apply -f https://liyuan3210.github.io/liyuan3210/resources/file/k8s/kube-flannel.yml				//资源保存
				
			如果是使用自己阿里docker镜像源,kube-flannel.yml下载下来后应该配置如下两项:
				apiVersion: apps/v1
					kind: DaemonSet
					metadata:
					  name: kube-flannel-ds-amd64
					....
					spec:
						  initContainers:
						  - name: install-cni
							image: registry.cn-shanghai.aliyuncs.com/kubernetes-3210/flannel:v0.12.0-amd64	//前面修改为registry.cn-shanghai.aliyuncs.com/kubernetes-3210
							...
						  containers:
						  - name: kube-flannel
							image: registry.cn-shanghai.aliyuncs.com/kubernetes-3210/flannel:v0.12.0-amd64	//前面修改为registry.cn-shanghai.aliyuncs.com/kubernetes-3210
				
			问题:The connection to the server raw.githubusercontent.com was refused - did you specify the right host or port?
				网上貌似说是主机不通问题导致:https://www.cnblogs.com/oscarli/p/12737409.html
				https://www.cnblogs.com/double-dong/p/11483670.html	//修改flannel镜像仓库地址
				解决办法:
				# 在https://www.ipaddress.com/查询raw.githubusercontent.com的真实IP。
				sudo vim /etc/hosts
				199.232.28.133 raw.githubusercontent.com
				
			验证部署成功
				kubectl get pods -n kube-system
				
		5>.测试命令
			kubectl get cs
			kubectl get componentstatus
			kubectl get nodes	//查看节点
			kubectl get pods -n kube-system	//查看pods下kube-system名称空间
			kubectl get ns //查看所有名称空间
			
		6>根据提示就可以在从节点加入进去
		kubeadm join 192.168.122.107:6443 --token o9eof4.btv2wqcdamlbfn7k \
			--discovery-token-ca-cert-hash sha256:0bde811f20a0195ec2313eef661b48f2891791e59b20612987455f58dc64ba4c
			
		7>超过24小时从新生成token
			https://www.cnblogs.com/xiaoyaojinzhazhadehangcheng/p/11605934.html
			重新初始化token(token 24小时后过期，超过时间需要重新获取)
			一步一来:
				token重新获取
				kubeadm token create		//生成token
					>> kiyfhw.xiacqbch8o8fa8qj
				kubeadm token list			//查看
				
				生成ca的sha256 hash值
				openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
					>> 5417eb1b68bd4e7a4c82aded83abc55ec91bd601e45734d6aba85de8b1ebb057
					
				组装命令:
					kubeadm join 192.168.122.107:6443 --token kiyfhw.xiacqbch8o8fa8qj \
						--discovery-token-ca-cert-hash sha256:5417eb1b68bd4e7a4c82aded83abc55ec91bd601e45734d6aba85de8b1ebb057
			一条命令完成上面操作:
				# 一步完成以上步骤
				kubeadm token create --print-join-command
			手动生成token，完成命令打印:
				token=$(kubeadm token generate)
				kubeadm token create $token --print-join-command --ttl=0
			
三.从节点安装
	1.安装docker
		yum install -y yum-utils device-mapper-persistent-data lvm2	//用到就装
		yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
		yum install -y https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/edge/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
		yum install -y docker-ce-19.03.9 docker-ce-cli-19.03.9
		docker服务自启,启动
			systemctl enable docker
			systemctl start docker
		docker验证
			docker pull hello-world
			docker run hello-world
			
		注意：
		根据官方文档可以看到最新release版本19.03.9,(安装我们也安装最新的)
		https://docs.docker.com/engine/release-notes/
			
	2.安装kubeadm
		配置源
		cat <<EOF > /etc/yum.repos.d/kubernetes.repo
		[kubernetes]
		name=Kubernetes
		baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
		enabled=1
		gpgcheck=1
		repo_gpgcheck=1
		gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
		EOF

		###setenforce 0
		yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
		###systemctl enable kubelet && systemctl start kubelet
		
		注意:
		根据官方文档可以看到最新release版本1.18.0 ,(安装我们也安装最新的)
		https://kubernetes.io/docs/setup/release/notes/#server-binaries
		
		systemctl enable kubelet
		###systemctl start kubelet		//启动docker服务,开始启动kubelet可能会启动不成功(初始化时会自动启动)
		
	3.加入主节点
		kubeadm join 192.168.122.107:6443 --token amj4dy.cw4bwztapgbfy9n3 --discovery-token-ca-cert-hash sha256:28e9ba2b6495373ab16299ad48c4db70a42857a984561097bc3ad5eef6328102
		
四.web-ui-dashboard安装
	官方
	https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
	https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
	实例
	https://www.jianshu.com/p/be2a12a8bc0b

	github
	https://github.com/kubernetes/dashboard
	实例
	https://www.cnblogs.com/linuxws/p/10657786.html
	删除
	https://blog.csdn.net/qq_39562468/article/details/103026958
	kubectl delete -f kubernetes-dashboard.yaml
	yarm文件不一样
	https://www.kubernetes.org.cn/4058.html
	
	
	service配置修改(recommended.yaml文件)
	1.添加type: NodePort与nodePort: 30001
			kind: Service
				apiVersion: v1
				metadata:
				  labels:
					k8s-app: kubernetes-dashboard
				  name: kubernetes-dashboard
				  namespace: kubernetes-dashboard
				spec:
				  type: NodePort
				  ports:
					- port: 443
					  targetPort: 8443
					  nodePort: 30001
				  selector:
					k8s-app: kubernetes-dashboard、
	2.修改默认源
	image: registry.aliyuncs.com/google_containers/metrics-scraper:v1.0.4
	image: registry.aliyuncs.com/google_containers/dashboard:v2.0.0
	
	3.启动服务
		kubectl apply -f  recommended.yaml
		//kubectl apply -f  recommended.yaml creating_account.yaml creating_cluster_role.yaml
		
	4.查看服务状态
		kubectl get svc -n kubernetes-dashboard
		
	5.外网访问
		实例
		https://blog.haohtml.com/archives/19201
		安装kubectl
		yum install -y kubectl-1.18.0
		nohup kubectl proxy --address='0.0.0.0' --port=8888 --accept-hosts='^*$'
		
		kubectl proxy --address='0.0.0.0' --port=8888 --accept-hosts='^*$'
		
		问题
		https://www.cnblogs.com/RainingNight/p/deploying-k8s-dashboard-ui.html
		
	资源文件
	https://liyuan3210.github.io/liyuan3210/resources/file/k8s/recommended.yaml
	https://liyuan3210.github.io/liyuan3210/resources/file/k8s/creating_account.yaml
	https://liyuan3210.github.io/liyuan3210/resources/file/k8s/creating_cluster_role.yaml
	
	
五.镜像操作
	1.阿里云镜像登录
	sudo docker login --username=liyuan3210 registry.cn-shanghai.aliyuncs.com
	
	2.批量同步
	#!/bin/bash	
	docker tag registry.aliyuncs.com/google_containers/kube-proxy:v1.18.0 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-proxy:v1.18.0
	docker tag registry.aliyuncs.com/google_containers/kube-apiserver:v1.18.0 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-apiserver:v1.18.0
	docker tag registry.aliyuncs.com/google_containers/kube-scheduler:v1.18.0 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-scheduler:v1.18.0
	docker tag registry.aliyuncs.com/google_containers/kube-controller-manager:v1.18.0 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-controller-manager:v1.18.0
	docker tag registry.aliyuncs.com/google_containers/pause:3.2 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/pause:3.2
	docker tag registry.aliyuncs.com/google_containers/coredns:1.6.7 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/coredns:1.6.7
	docker tag registry.aliyuncs.com/google_containers/etcd:3.4.3-0 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/etcd:3.4.3-0
	docker tag quay.io/coreos/flannel:v0.12.0-amd64 registry.cn-shanghai.aliyuncs.com/kubernetes-3210/flannel:v0.12.0-amd64

	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-proxy:v1.18.0
	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-apiserver:v1.18.0
	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-scheduler:v1.18.0
	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/kube-controller-manager:v1.18.0
	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/pause:3.2
	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/coredns:1.6.7
	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/etcd:3.4.3-0
	docker push registry.cn-shanghai.aliyuncs.com/kubernetes-3210/flannel:v0.12.0-amd64
	
	3.指定自己仓库安装kubeadm (registry.cn-shanghai.aliyuncs.com/kubernetes-3210)
	kubeadm init --image-repository registry.cn-shanghai.aliyuncs.com/kubernetes-3210 --apiserver-advertise-address=192.168.122.107 --kubernetes-version=v1.18.0 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12


4.国内加速镜像
	https://www.cnblogs.com/zhangrui153169/p/12699077.html
	Docker中国区官方镜像
	https://registry.docker-cn.com

	网易
	http://hub-mirror.c.163.com

	ustc 
	https://docker.mirrors.ustc.edu.cn

	中国科技大学
	https://docker.mirrors.ustc.edu.cn

	阿里云容器  服务
	https://cr.console.aliyun.com/
	首页点击“创建我的容器镜像”  得到一个专属的镜像加速地址，类似于“https://1234abcd.mirror.aliyuncs.com”

	腾讯加速地址:https://mirror.ccs.tencentyun.com
	
	vi /etc/docker/daemon.json
	{
	  "registry-mirrors": ["https://6brt8p5b.mirror.aliyuncs.com"]
	}
	
	配置后重启
	systemctl restart docker

问题：
[preflight] Running pre-flight checks
[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[WARNING FileExisting-tc]: tc not found in system path
[WARNING Hostname]: hostname "centos8" could not be reached
[WARNING Hostname]: hostname "centos8": lookup centos8 on 192.168.122.1:53: no such host


5.根据现有制作提交镜像
	docker login --username=liyuan3210 registry.cn-shanghai.aliyuncs.com		//输入阿里账号
	docker pull centos	//拉取最新centos
	docker tag centos:latest registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/centos:init	//打tag
	docker push registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/centos:init	//提交
	
	根据容器创建镜像
	docker commit -m "添加java" -a "liyuan" 4d561ca065a6 registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/centos:1.0.0
	提交镜像
	docker push registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/centos:1.0.0
	
	运行容器
	docker run --restart=always --name test -it registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/centos:1.0.0
	

六.k8s高可用HA部署
...


	
常见问题:
		常见问题汇总
		https://www.cnblogs.com/liuyi778/p/12229416.html
		
		问题1:.centos 报错failed to load SELinux policy
		是因为vi /etc/sysconfig/selinux参数配置错误，导致系统进不去.
		需要开机进入grub,按e编辑,配置临时启动参数在linux ($root)vmlinuz-xxx左边任意位置
		添加(selinux=0 或者 enforcing=0),按ctrl+x重新引导,然后再次修改selinux文件即可
		http://www.mamicode.com/info-detail-1847013.html
		
		问题2:The connection to the server raw.githubusercontent.com was refused - did you specify the right host or port?
		网上貌似说是主机不通问题导致:https://www.cnblogs.com/oscarli/p/12737409.html
		解决办法:
		# 在https://www.ipaddress.com/查询raw.githubusercontent.com的真实IP。
		sudo vim /etc/hosts
		199.232.28.133 raw.githubusercontent.com
		
		问题detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "
		https://www.jianshu.com/p/f53650a85131
		{
		  "exec-opts": ["native.cgroupdriver=systemd"]
		}
```

### 二.kubeadm安装

```
一.安装配置

kubernetes两种安装方式：
	1.API server , Scheduler , Controller Manager ,ectd运行在单独node节点上的应用程序
	2.API server , Scheduler , Controller Manager ,ectd以docker容器类型运行在master节点上(利用kubeadm工具)
	kubeadm可以运行直接部署3个master做冗余

1.准备环境：
	master
	node1
	node2
	必须环境：
	1>.配置好host与免密登录ssh(参考hadoop)
	2>.注意每个节点时间同步问题
	3>.关闭防火墙
		systemctl status firewalld.service	//查看防火墙状态
		systemctl stop firewalld.service	//停止防火墙
		systemctl disable firewalld.service	//禁止防火墙开机启动
	4>.关闭swap
		swapoff -a
		再把/etc/fstab文件中带有swap的行注释。
		#/dev/mapper/centos-swap swap                    swap    defaults        0 0
	5>.关闭SELinux
		setenforce 0	(设置SELinux 成为permissive模式 临时关闭selinux的)
		vi /etc/sysconfig/selinux
		SELINUX=disabled		//修改SELINUX属性

2.centos yum源配置与安装:
	1>.添加docker-ce源
		/etc/yum.repos.d/
		自动拉取源docker
		wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

	2>.添加kubernetes源(手动编写kubernetes.repo文件)
		[kubernetes]
		name=kubernetes repo
		baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
		gpgcheck=1	//1表示检查,0表示不检查
		gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
		enabled=1

			安装出现问题检查key问题
				上面的貌似可以不用导入(手动导入)
					wget https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
					rpm --import rpm-package-key.gpg

3.查看可安装版本,并指定版本安装(下面操作都在master进行):
	1>.测试仓库源
		yum repolist
			生成缓存
			yum makecache
			安装缓存
			yum -y update

	2>.查看能安装的版本
		kubelet kubeadm kubectl三个版本都为1.15.3
		yum list kubelet kubeadm kubectl  --showduplicates|sort -r
		docker-ce版本为18.06.3.ce-3.el7
		yum list docker-ce --showduplicates|sort -r

	3>.指定并版本安装
		--(centos7)安装18.06
			yum install -y docker-ce-18.06.3.ce-3.el7
			yum install -y kubelet-1.15.3 kubeadm-1.15.3 kubectl-1.15.3
		--(centos8)安装docker19.03
			yum install -y yum-utils device-mapper-persistent-data lvm2	//用到就装
			yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
			yum install https://mirrors.aliyun.com/docker-ce/linux/centos/7/x86_64/edge/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
			yum install docker-ce docker-ce-cli
			
		设置开机自启动
			systemctl enable kubelet docker
			systemctl start docker		//启动docker服务,开始启动kubelet可能会启动不成功(初始化时会自动启动)
			
		docker验证
			docker pull hello-world
			docker run hello-world
4.安装kubeadm镜像
	1>.查看该版本需要的容器镜像版本
		kubeadm config images list
		结果
		k8s.gcr.io/kube-apiserver:v1.15.3
		k8s.gcr.io/kube-controller-manager:v1.15.3
		k8s.gcr.io/kube-scheduler:v1.15.3
		k8s.gcr.io/kube-proxy:v1.15.3
		k8s.gcr.io/pause:3.1
		k8s.gcr.io/etcd:3.3.10
		k8s.gcr.io/coredns:1.3.1
	2>.下载镜像
		一般默认执行kubeadm init初始化时kubeadm是从k8s.gcr.io站点pull下载
		由于境外网站已被墙,所以有两种方法解决这个问题：
		1>>.设置docker镜像站点代理执行kubeadm init自动pull下载
			vi /usr/lib/systemd/system/docker.service
			Environment="HTTPS_PROXY=http://www.ik8s.io:10080"
			Environment="NO_PROXY=127.0.0.0/8,172.20.0.0/16"	//本地网络就不用代理
			systemctl daemon-reload //重新生效
		2>>.手动pull时指定镜像服务地址进行,
		本次采取手动pull并指明镜像地址,编写shell下载pull
		
		指明azure镜像站点,pull上面列出的容器与版本
		http://mirror.azure.cn/help/gcr-proxy-cache.html
		
		kebu-install.sh
			#!/bin/bash
			docker pull gcr.azk8s.cn/google_containers/kube-apiserver:v1.15.3
			docker pull gcr.azk8s.cn/google_containers/kube-controller-manager:v1.15.3
			docker pull gcr.azk8s.cn/google_containers/kube-scheduler:v1.15.3
			docker pull gcr.azk8s.cn/google_containers/kube-proxy:v1.15.3
			docker pull gcr.azk8s.cn/google_containers/pause:3.1
			docker pull gcr.azk8s.cn/google_containers/etcd:3.3.10
			docker pull gcr.azk8s.cn/google_containers/coredns:1.3.1

			docker tag gcr.azk8s.cn/google_containers/kube-apiserver:v1.15.3 k8s.gcr.io/kube-apiserver:v1.15.3
			docker tag gcr.azk8s.cn/google_containers/kube-controller-manager:v1.15.3 k8s.gcr.io/kube-controller-manager:v1.15.3
			docker tag gcr.azk8s.cn/google_containers/kube-scheduler:v1.15.3 k8s.gcr.io/kube-scheduler:v1.15.3
			docker tag gcr.azk8s.cn/google_containers/kube-proxy:v1.15.3 k8s.gcr.io/kube-proxy:v1.15.3
			docker tag gcr.azk8s.cn/google_containers/pause:3.1 k8s.gcr.io/pause:3.1
			docker tag gcr.azk8s.cn/google_containers/etcd:3.3.10 k8s.gcr.io/etcd:3.3.10
			docker tag gcr.azk8s.cn/google_containers/coredns:1.3.1 k8s.gcr.io/coredns:1.3.1

			docker rmi gcr.azk8s.cn/google_containers/kube-apiserver:v1.15.3
			docker rmi gcr.azk8s.cn/google_containers/kube-controller-manager:v1.15.3
			docker rmi gcr.azk8s.cn/google_containers/kube-scheduler:v1.15.3
			docker rmi gcr.azk8s.cn/google_containers/kube-proxy:v1.15.3
			docker rmi gcr.azk8s.cn/google_containers/pause:3.1
			docker rmi gcr.azk8s.cn/google_containers/etcd:3.3.10
			docker rmi gcr.azk8s.cn/google_containers/coredns:1.3.1
		
	3>.查看确认为1
		cat /proc/sys/net/bridge/bridge-nf-call-ip6tables
		cat /proc/sys/net/bridge/bridge-nf-call-iptables
		
	4>.配置kubelet
		vi /etc/sysconfig/kubelet
		KUBELET_EXTRA_ARGS="--fail-swap-on=false"
		
5.初始化
		1>.初始化命令(初始化后会自动启动kubelet)
			kubeadm init --kubernetes-version=v1.15.3 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --ignore-preflight-errors=Swap
			kubeadm init --kubernetes-version=v1.15.3 --ignore-preflight-errors=Swap	//测试这个
	
		2>.初始化节点成功
			Your Kubernetes control-plane has initialized successfully!

			To start using your cluster, you need to run the following as a regular user:

			  mkdir -p $HOME/.kube
			  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
			  sudo chown $(id -u):$(id -g) $HOME/.kube/config

			You should now deploy a pod network to the cluster.
			Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
			  https://kubernetes.io/docs/concepts/cluster-administration/addons/

			Then you can join any number of worker nodes by running the following on each as root:

			kubeadm join 192.168.122.186:6443 --token 971tyu.9zdt8xx1ys0lifkt \
				--discovery-token-ca-cert-hash sha256:cdb7d7827d87f8ae547664939e5acc40905e8ad1d351cffc1df9e4057be9cb5b
				
			重新初始化token(token 24小时后过期，超过时间需要重新获取)
				token重新获取
				kubeadm token list
				或者 
				kubeadm token create
				
				sha256获取方式 master节点执行:
				openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'

	     3>.安装Flannel
			项目地址:https://github.com/coreos/flannel
				kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml		//最新v0.11.0
				kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/v0.11.0/Documentation/kube-flannel.yml	//指定版本
			验证部署成功
				kubectl get pods -n kube-system
		 4>.测试命令
			kubectl get cs
			kubectl get componentstatus
			kubectl get nodes	//查看节点
			kubectl get pods -n kube-system	//查看pods下kube-system名称空间
			kubectl get ns //查看所有名称空间
		
		查看错误信息：
			systemctl status kubelet //查看
			tail /var/log/message	//查看日志
6.k8s管理界面
	https://github.com/kubernetes/dashboard
	部署安装
	https://cloud.tencent.com/developer/article/1425500
--------------------------------------------------
rpm -ql kubelet
查看与启动kubernetes
	设置KUBELET_EXTRA_ARGS="--fail-swap-on=false"
	vi /etc/sysconfig/kubelet
	systemctl start kubelet	//启动
	systemctl status kubelet //查看
	tail /var/log/message	//查看日志
	设置开机自启动
	systemctl enable kubelet docker

初始化
kubeadm init --kubernetes-version=v1.11.1 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --ignore-preflight-errors=Swap
	初始化成功后,想要启动master需要执行提示的命令,
	还会产生的token脚本(需要保存下来在node节点执行,在节点执行前必须执行提示的脚本)
	token后面还需要加上配置(在node节点执行的):kubeadm join 172... --ignore-preflight-errors=Swap
--------------------------------------------------
6.添加node节点
	1>.拷贝文件
		scp /etc/yum.repos.d/docker-ce.repo root@node2:/etc/yum.repos.d/
		scp /etc/yum.repos.d/kubernetes.repo root@node2:/etc/yum.repos.d/
		scp /etc/sysconfig/kubelet root@node2:/etc/sysconfig/
	2>.安装程序
		yum install -y docker-ce-18.06.3.ce-3.el7
		yum install -y kubelet-1.15.3 kubeadm-1.15.3
	3>.启动服务及设置自启动docker
		systemctl start docker
		systemctl enable docker
	
	4>.安装子节点镜像
		#!/bin/bash
		docker pull gcr.azk8s.cn/google_containers/kube-proxy:v1.15.3
		docker pull gcr.azk8s.cn/google_containers/pause:3.1

		docker tag gcr.azk8s.cn/google_containers/kube-proxy:v1.15.3 k8s.gcr.io/kube-proxy:v1.15.3
		docker tag gcr.azk8s.cn/google_containers/pause:3.1 k8s.gcr.io/pause:3.1

		docker rmi gcr.azk8s.cn/google_containers/kube-proxy:v1.15.3
		docker rmi gcr.azk8s.cn/google_containers/pause:3.1
		
		//貌似还有quay.io/coreos/flannel 镜像,但上面可以安装成功
		
	5>.自启动kubelet
		systemctl start kubelet
		systemctl enable kubelet
		systemctl enable kubelet docker	//设置开机自启动

	5>.加入子节点(后面添加 --ignore-preflight-errors=Swap)
		kubeadm join 192.168.122.186:6443 --token 971tyu.9zdt8xx1ys0lifkt \
			--discovery-token-ca-cert-hash sha256:cdb7d7827d87f8ae547664939e5acc40905e8ad1d351cffc1df9e4057be9cb5b --ignore-preflight-errors=Swap
			
	6>.加入成功
		[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

		This node has joined the cluster:
		* Certificate signing request was sent to apiserver and a response was received.
		* The Kubelet was informed of the new secure connection details.

		Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
--------------------------------------------------
7.初始化错误,及错误解决办法

1>重新初始化
	https://blog.csdn.net/lansye/article/details/79984077
	1>>.停止服务
		systemctl stop kubelet

	2>>.删除所有容器
		docker rm -f $(docker container ls -aq)

	3>>.删除目录
		删除/var/lib/kubelet/目录
		删除/var/lib/rancher/目录
		删除/run/kubernetes/ 目录

	4>>.这边有个坑，转发规则如果不清除，导致新的网络不通[没试过,]
		iptables -F
		iptables -X
		iptables -L
		find / -type f -name docker*
		find / -type f -name calico*
		find / -type f -name etcd*
		shutdown -r  now

2>.报错问题解决
https://blog.csdn.net/zhd930818/article/details/79644903

1>>.问题1
	[ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]:
	/proc/sys/net/bridge/bridge-nf-call-iptables contents are not set to 1

	https://www.cnblogs.com/jackluo/p/5422243.html
	在CentOS中
	vim /etc/sysctl.conf 
	net.bridge.bridge-nf-call-ip6tables = 1
	net.bridge.bridge-nf-call-iptables = 1
	net.bridge.bridge-nf-call-arptables = 1


2>>.问题2
[ERROR DirAvailable--var-lib-etcd]: /var/lib/etcd is not empty
	解决
	rm -rf /var/lib/etcd

3>>.问题3
	Initial timeout of 40s passed
	https://blog.csdn.net/qq_19107011/article/details/92802571


node1节点(其它节点重复node1步骤)
rpm --import rpm-package-key.gpg
yum install docker-ce kublet kubeadm (node不需要客户端可以不装kubectl)

systemctl start docker

systemctl enable kubelet docker	//设置开机自启动

kubeadm join 172... --ignore-preflight-errors=Swap

--------------------------------------------------

问题
1.kubectl join 时候
warning: /etc/sysconfig/kubelet created as /etc/sysconfig/kubelet.rpmnew
2.master安装时
--ignore-preflight-errors=Swap
3.kubelet需不需要启动
systemctl start kubelet
systemctl enable kubelet
4.超过一天后token生成
5.pods网络不通(解决)

----------------------------------------------------------------------------------------------------
kubeadm集群安装(镜像源)
https://www.cnblogs.com/Irving/p/9818440.html
使用说明
https://anjia0532.github.io/2017/11/15/gcr-io-image-mirror/

采坑总结
https://blog.csdn.net/weixin_42388901/article/details/86543947

kubernetes手动集群配置安装
https://www.jianshu.com/p/214cfeb12ad3

docker.io无法访问问题
https://blog.csdn.net/hahaxu/article/details/80522244

azure镜像下载(可以下载)
http://mirror.azure.cn/help/gcr-proxy-cache.html
清华镜像站点
https://mirrors.tuna.tsinghua.edu.cn/
阿里镜像站点
https://opsx.alibaba.com/mirror

安装时出现问题阻塞20190904
https://www.jianshu.com/p/851de5a6fef4
--------------------------------------------镜像测试
docker pull anjia0532/google-containers/kube-apiserver:v1.15.3
docker pull anjia0532/google-containers.kube-apiserver:v1.12.1

docker pull mirrorgooglecontainers/kube-apiserver:v1.15.3

docker pull registry.cn-hangzhou.aliyuncs.com/k8sth/kube-apiserver:v1.15.3

registry-1.docker.io
auth.docker.io

docker pull hub.docker.com/r/anjia0532/google-containers.kube-apiserver:v1.15.3

docker pull gcr.azk8s.cn/google_containers/kube-apiserver:v1.15.3



kubeadm cluster installl集群安装（同步时间）
https://blog.csdn.net/clouduncle/article/details/82750291
```

