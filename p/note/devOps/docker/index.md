# docker

课程：

https://www.bilibili.com/video/BV18b411K7q7?from=search&seid=3006540982052779079

**二进制安装：**

https://docs.docker.com/engine/install/binaries/#install-static-binaries

配置selinux

https://blog.csdn.net/java0506/article/details/108600504

## 一.docker安装

```
官网：https://www.docker.com
	有两个版本
	ce社区版与ee企业版
	
windows安装
	https://github.com/boot2docker
	包含如下组件：
	1.virtualbox
	2.msys-git
	3.boot2docker 
	4.Linux 镜像
	5.Boot2Docker 的管理工具
	
下面两种方式安装都要关闭SELinux
		setenforce 0	(设置SELinux 成为permissive模式 临时关闭selinux的)
		或
		vi /etc/sysconfig/selinux
		SELINUX=disabled		//修改SELINUX属性
	
一.yum方式安装
	https://docs.docker.com/engine/install/centos/
	1.添加docker安装yum源
        $ sudo yum install -y yum-utils
        $ sudo yum-config-manager \
            --add-repo \
            https://download.docker.com/linux/centos/docker-ce.rep
            
    2.查看指定docker版本
    	$ yum list docker-ce --showduplicates | sort -r
    	
    3.安装docker指定版本
    	$ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
    	
   	4.启动服务/自启
   		$ sudo systemctl start docker
   	5.验证
		$ sudo docker run hello-world
   	6.反安装
   		$ sudo yum remove docker-ce docker-ce-cli containerd.io
   		$ sudo rm -rf /var/lib/docker
    
二.二进制安装
	https://docs.docker.com/engine/install/binaries/
	1.下载二级制包并解压
		$ tar xzvf /path/to/<FILE>.tar.gz
	2.拷贝安装到/usr/bin/
		$ sudo cp docker/* /usr/bin/
	3.启动docker服务
		$ sudo dockerd &
   	4.验证
		$ sudo docker run hello-world
		
三.镜像私服安装registry
	https://docs.docker.com/registry/#basic-commands	//官方安装步骤
	https://docs.docker.com/registry/deploying/
	1.启动并注册
        $ docker pull registry:2
        $ docker run -d -p 5000:5000 -v /var/lib/registry:/var/lib/registry --restart=always --name registry registry:2
            -v 宿主机目录:容器目
    2.验证与测试
    	浏览器查看
		http://192.168.127.130:5000/v2/_catalog

		查看hw仓库tag
		http://192.168.127.130:5000/v2/hw/tags/list
	
		删除仓库镜像(官方是不建议删除的,因为会导致依赖的容器无法运行):
			直接进入/var/lib/registry/docker/registry/v2/repositories目录,
			删除仓库名即可
			
        获取digest值
        curl -I -X GET http://192.168.127.130:5000/v2/hw/manifests/latest

        curl -X DELETE http://192.168.127.130:5000/v2/hw/manifests/sha256:9abfef7f7c24a0ea1a7736b90a180d1d1ff8c880aa37205ded5541e004f6f04b

        相同问题解决方法
            http://www.itkeyword.com/doc/5815940265600701490/why-cant-i-delete-a-layer-in-my-private-docker-registryv2

        私有仓库搭建
        	https://www.linuxidc.com/Linux/2017-02/141054.htm
        	
四.客户端配置文件（加速器配置）
	1.加速器配置(centos)
        腾讯加速地址:https://mirror.ccs.tencentyun.com
        vi /etc/docker/daemon.json
        {
          "registry-mirrors": ["https://6brt8p5b.mirror.aliyuncs.com"]
        }
	
        配置后重启
        systemctl restart docker

	2.ubuntu配置
		registry服务端默认开启的是http访问,而客户端默认访问是https:
        需要在客户端/etc/docker/daemon.json添加如下内容(没有此文件需要新增)
        { "insecure-registries":["192.168.127.130:5000"] }
        然后重启客户端服务
        /etc/init.d/docker restart

        ubuntu下docker配置文件
        	vi /etc/default/docker

        ubuntu默认安装目录：
        	/var/lib/docker

        启动/停止docker后台服务
            service docker start
            service docker stop
```

## 二.docker镜像管理

```
一.常用命令
	docker images					//显示所有镜像
	docker pull nginx 				//拉取nginx镜像
	docker rmi hello-world			//删除一个镜像,直接删除(docker rmi hello-world -f)
	docker search nginx				//查找镜像
	
二.镜像托管
1.docker官网默认仓库登录
docker在线查看镜像网站(官网)
https://hub.docker.com登录密码
仓库登录：
docker login	//默认登录官网仓库,输入hub.docker.com登录密码
仓库登出：
docker logout

2.阿里/腾讯镜像操作：
	密码见www-dev
	阿里:
        https://cr.console.aliyun.com	//管理控制台
        sudo docker login --username=liyuan3210 registry.cn-shanghai.aliyuncs.com	//仓库登录,私有拉取,push时需要密码，密码可以单独设置
        docker tag nginx:latest registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/nginx:latest	//打tag
        docker push registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/nginx:latest	//上传
	
	腾讯：
        https://console.cloud.tencent.com/tke2	//管理控制台
        sudo docker login --username=465049568 ccr.ccs.tencentyun.com	//仓库登录,私有拉取,push时需要密码，密码可以单独设置
        docker tag centos:v1.0.0 ccr.ccs.tencentyun.com/liyuan3210-repo/centos:v2.0.0 //打tag
        docker push ccr.ccs.tencentyun.com/liyuan3210-repo/centos:v2.0.0	//上传
        
三.镜像导出,导入
    docker镜像导入导出(save,load,export,import)
    https://blog.csdn.net/ncdx111/article/details/79878098
     1.save与load(备份,导入images)
         save:
                示例 
                docker save -o nginx.tar nginx:latest 
                或 
                docker save > nginx.tar nginx:latest 
         load:
                示例
                docker load -i nginx.tar
                或
                docker load < nginx.tar
        
     2.export与import(备份导入容器)
         export:
                docker export -o nginx-test.tar nginx-test
         import:
                示例
                docker import nginx-test.tar nginx:imp
                或
                cat nginx-test.tar | docker import - nginx:imp
                
     区别：
     *export命令导出的tar文件略小于save命令导出的
     *export命令是从容器（container）中导出tar文件，而save命令则是从镜像（images）中导出
     *基于第二点，export导出的文件再import回去时，无法保留镜像所有历史（即每一层layer信息，不熟悉的可以去看Dockerfile），不能进行回滚操作；而save是依据镜像来的，所以导入时可以完整保留下每一层layer信息。如下图所示，nginx:latest是save导出load导入的，nginx:imp是export导出import导入的。
     
三.基于容器做镜像
	docker commit -m="描述信息" -a="作者" 容器id 要创建镜像名称:标签

四.镜像上传:
	1.打tag
	docker tag ubuntu:16.04.3 192.168.209.132:5000/ubuntu:v1 //修改为规范的镜像
	2.推送
	docker push 192.168.209.132:5000/ubuntu:v1	//推送	
```

## 三.docker容器操作

```
一.基础命令
交互式容器
	docker run -it centos 	//--name可省略
	docker run --name demo1 -i -t ubuntu:15.10 /bin/bash	//创建容器并进入,命名容器demo1
	docker run --name demo1 -it ubuntu:15.10 /bin/bash		//-i -t 可以合并写成 -it
	docker run --h //自定义容器主机名
	退出：exit或CTRL+D
		 ctrl+P+Q	（容器不停止退出）
		 批量删除镜像，容器

	启动容器(后台)
	docker run -d centos 	//后台启动过，生成hash容器id，并立刻退出(docker后台运行必须要有个前台进程)
		docker run -d centos /bin/bash -c "while true; do echo hello world; sleep 3; done"
	查看容器日志	docker logs -f -t --tail 容器id
		docker run -it centos /bin/bash	//`/bin/bash`可以不用添加，因为镜像dockerfile里面默认就有了
	查看容器运行进程：
		docker top 4a3adf
	查看容器内部细节
		docker inspect 4a3adf
	进入正在运行容器并以命令交互
		docker attach 4a3adf	//进入容器,貌似只有用-it创建的容器才可以这样进去
		docker exec -t 4a3adf ls -l /	//直接拿出容器命令执行结果
				docker exec -it 4a3adf /bin/bash	//有点类似attach,但非-it创建的容器都可以进去
	从容器内拷贝文件到主机
	docker cp 4a3adf:/tmp/yum.log /yum.log
	
	
	docker run -it -p 8080:8080 centos
		-p 暴露端口:docker内部端口
		-P	随机端口
	docker run -it centos	//打印日志,加-d可以后台启动
	
	基于容器副本制作成一个新的镜像
	docker commit -m="描述信息" -a="作者" 容器id 要创建镜像名称:标签
	
二.容器数据卷
容器数据卷（目录自动创建）：
		dockerfile文件名称dockerfile2
		FROM centos
		VOLUME ["/data1","/data2"]
		CMD echho "finished,------success"
		CMD /bin/bash
		
		docker build -f dockerfile2 -t ly/centos .
		
		命令行
		docker run -it -v /宿主机绝对路径目录:/容器内路径目录 镜像名称
		dockerfile命令指定：
			VOLUME ["/data1","/data2"]
			有个宿主机有个默认目录(使用inspect查看):/var/lib/docker/volumes/
			
		数据卷容器(容器间传递共享)--volumes-from
			启动一个父容器,在data2里面新增内容
			docker run -it --name dc01 ly/centos
			新建dco2容器继承dc01
			docker run -it --name dc02 --volumes-from dco1 ly/centos
			新建dco3容器继承dc01
			docker run -it --name dc03 --volumes-from dco1 ly/centos	
			
			父类，子类 文件修改都会相互同步.
			删除父，子容器，也不会影响关联的容器
```

## 四.dockerfile

```
dockerfile解析
			每条指令都会创建一个新的镜像层
		指令集
			FROM 基础镜像
			MAINTAINER 作者+作者邮箱
			RUN 容器构建时需要运行的命令
			EXPOSE 暴露端口号
			WORKDIR 登录docker时的工作目录
			EVN 用来构建过程中设置环境变量
			COPY 直接拷贝 
			ADD(类似COPY,但比COPY强大) 拷贝+自解压缩
			VOLUME 容器数据卷
			CMD(与RUN类似) 指定容器运行时要运行的命令，只有最后一个生效，会被docker run 之后参数替换
			ENTRYPOINT(与CMD类似) CMD，但docker run 之后参数替换，但ENTRYPOINT会被追加
			ONBUILD 当构建一个被继承的dockerfile时运行命令，父镜像在被子集成后父镜像的onbuild被触发
			
dockerfile构建语法：
	$ docker build -t 新镜像名字:tag .
	$ docker build -f dockerfile文件名字 -t mycentos:v1.0.0 .
```


## 五.docker命令使用

```
一.容器
	容器中运行应用程序
	sudo docker run ubuntu:15.10 /bin/echo "Hello world" //创建容器并进入

	docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
	-d表示后台运行
	2b1b7a428627c51ab8810d541d759f072b4fc75487eed05812646b8534a2fe63

	docker run --name testweb2 -e MYNAME=liyuan3210 -d 192.168.10.23:5000/testweb:V1 /bin/bash

	docker run --name testweb2 -e MYNAME=liyuan3210 -e MYPWD=123456 -d 192.168.10.23:5000/testweb:V1 /bin/bash

	测试
	curl http://[continer IP]
	

4.查看容器
	docker ps	//查看启动的容器
	docker ps -a //查看所有容器

	查看进程号
	docker  top [contianer ID/contianer name]

	执行命令
	docker exec [contianer ID/contianer name]
	docker exec -it [contianer ID/contianer name] sh   #交互式操作

5.查看容器输出
	docker logs 2b1b7a428627
	docker logs -f
	docker logs --tail <number>

6.容器操作
	docker attach 2b1b7a428627 //进入前台容器,后台ctrl+p+q	

	docker inspect 2b1b7a428627	//查看容器信息
	docker version
	docker info

	docker stop amazing_cori  //停止容器
	docker  start  amazing_cori  //启动容器
	docker  restart  amazing_cori  //重启动容器
	docker run --restart=always [image] [cmd] //自启动
	docker pause [contianer ID/contianer name]	//暂停
	docker unpause [contianer ID/contianer name] //恢复

	docker run -e var1=1234 -e var2=2123 -it docker.io/busybox sh	//容器参数？
	docker -p 3306:3316	 //容器与宿主机端口转换?

容器与宿主机之间相互cp文件
	docker cp [path/container path] [container path/path]

查看容器修改
	docker diff [contianer ID/contianer name]

移除容器
	docker rm amazing_cori
	docker rm -f amazing_cori	//删除正在运行的容器
	docker rm 'docker ps -a -q' [contianer ID/contianer name]	//删除所有容器?

Docker 网络类型
	birdge=vm的nat  容器网络通过nat方式传输
	host 容器网络使用宿主机网络
	none 容器无网络
	container 容器网络复制其他容器

实例:
	docker run --network host
	以host方式启动容器
	docker run --network container:[name]
	以container方式启动容器

	docker run --network none
	以无网络方式启动容器
	
	docker network create --driver bridge --subnet [ip/mask] --gateway [ip] [name]	//创建一个网络
	docker network ls	//显示所有网络
	
--link  #容器做地址链接
	 docker run --link [name]:[alias] 
	Ex:
	docker run  --link con1:web -it docker.io/busybox sh
		
	-p  #容器与宿主机端口映射
		docker run -p  [hostport]:[containerport]
	Ex:
		docker run -p 8080:80 -d docker.io/nginx  
		
二.Dockerfile构建
dockerfile
	docker build -t 192.168.127.130:5000/centos:v1 ./
	
	
数据库db(参考公司课程里面的db实例)

1.构建镜像
docker build -t 127.0.0.1:5000/centos:db_v1 ./

2.运行构建镜像的容器
docker run --name db1 -e MYPWD=ly123 -e MYDB=pro -e MYUSER=adm -e USERPWD=123 -it 127.0.0.1:5000/centos:db_v1 /bin/bash


三.硬件限制:
	cpu限制
	docker run -it --cpu-period --cpu-quota
	Ex: 限制使用0.5核cpu (cpu-quota/cpu-period)
	 docker run -it --cpu-period=100000 --cpu-quota=50000 docker.io/busybox sh

	mem限制
	docker run  -m
	docker run -it -m 100M docker.io/busybox sh

四.公司培训:
	wget http://10.20.202.161/busybox.tar

	配置 yum 源
	cd /etc/yum.repos.d && for r in *.repo; do mv $r $r.bak;done

	wget http://mirrors.aliyun.com/repo/Centos-7.repo
	yum clean all
	yum makecache 
	yum install docker -y

	yum install docker
	systemctl start docker
	systemctl enable docker

	公司:https://10.19.105.102/svn/Public/10微服务培训


	查看文件实际引用大小
	du -sh file
-----------------------------------------------二部分-----------------------------------------------------
安全
	秘钥
		创建密钥
			mkdir /certs
			openssl req -newkey rsa:4096 -nodes -sha256 -keyout /certs/domain.key -x509 -days 365 -out /certs/domain.crt
			注意servername配置，案例中将使用”certs.yunda.com”
			
		使用密钥
			REGISTRY_HTTP_ADDR=0.0.0.0:443 
			REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt 
			REGISTRY_HTTP_TLS_KEY=/certs/domain.key
			
		客户端配置
			cd /etc/docker/certs.d/
			mkdir certs.yunda.com
			cp /certs/domain.crt certs.yunda.com/ca.crt
			重要：目录名需要与之前配置主机名一致
			
		Ex:
		docker run -d --restart=always --name=registry -v /certs:/certs -v /var/lib/registry:/var/lib/registry -e REGISTRY_HTTP_ADDR=0.0.0.0:443 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key -p 443:443 registry

	认证
		创建认证文件
			htpasswd -Bbn [username] [password] >> [file]
			安装httpd
			Ex:
			mkdir auth
			htpasswd -Bbn user1 user1passwd >> auth/htpasswd
			
		使用认证文件
			认证类型：
			REGISTRY_AUTH=htpasswd
			认证描述信息
			REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm
			认证文件
			REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd

		Ex:
		docker run -d --restart=always --name=registry -v /certs:/certs -v /var/lib/registry:/var/lib/registry -v /root/auth/:/auth -e REGISTRY_HTTP_ADDR=0.0.0.0:443 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key -e "REGISTRY_AUTH=htpasswd" -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd -p 443:443 registry
		
		客户端配置:
			docker login certs.yunda.com
			docker [push|pull]

存储驱动:
	Aufs (目前支持ubuntu/debian)
	devicemapper [centos默认]
	Overlay 
	Btrfs  (suse)
	zfs
	
	Device Mapper是一个基于kernel的框架，它增强了很多Linux上的高级卷管理技术。Docker的devicemapper驱动在镜像和容器管理上，利用了该框架的超配和快照功能。
	商业支持的Docker Engine（CS-Engine）建议在RHEL和CentOS上使用devicemapper存储驱动。
	
	两大特性
	Snapshot快照/Cow (copy on write) 写时复制
	allocate-on-demand 用时分配
	
	cow
	基于一个image启动多个Container，如果为每个Container都去分配一个image一样的文件系统，那么将会占用大量的磁盘空间。而CoW技术可以让所有的容器共享image的文件系统，所有数据都从image中读取，只有当要对文件进行写操作时，才从image里把要写的文件复制到自己的文件系统进行修改。

	allocate-on-demand 用时分配
	只有在要新写入一个文件时才分配空间，这样可以提高存储资源的利用率。比如启动一个容器，并不会为这个容器预分配一些磁盘空间，而是当有新文件写入时，才按需分配新空间。

	查看当前容器存储
		[root@certs ~]# losetup  -a
		[root@certs ~]# lsblk 
		[root@certs ~]# dmsetup ls --tree
		[root@certs ~]# docker inspect [containerID]

	使用direct-lvm(动态扩容机制)
		pvcreate /dev/sdb
		vgcreate docker /dev/sdb
		lvcreate --wipesignatures y -n thinpool docker -l 90%VG 
		lvcreate --wipesignatures y -n thinpoolmeta docker -l 5%VG  
		lvconvert -y --zero n -c 512K --thinpool docker/thinpool --poolmetadata docker/thinpoolmeta
	
	使用direct-lvm
		mkdir -p /etc/lvm/profile/
		cat <<EOF > /etc/lvm/profile/docker-thinpool.profile
		activation {
		thin_pool_autoextend_threshold=80
		thin_pool_autoextend_percent=20
		}
		EOF
		
	使用direct-lvm
	lvchange --metadataprofile docker-thinpool docker/thinpool
	/etc/docker/daemon.json
	{
	    "storage-driver": "devicemapper",
	    "storage-opts": [ 
	        "dm.thinpooldev=/dev/mapper/docker-thinpool",
	        "dm.fs=ext4"
	    ]
	}

日志驱动
	开源免费的:
		syslog
		journald
		Json-file
		fluentd

	商业级
	Google cloud
	Splunk/elk
	ETW
	
	详细介绍：
	syslog本地日志
		docker run -d docker.io/busybox /bin/sh -c "while true ; do date ; sleep 1;  done”
		docker logs [containerID]
		docker logs -f [containerID]
		[root@certs html]# tail /var/log/messages -f //查看所有的
		
	远程syslog日志:
		--log-driver=syslog
		--log-opt syslog-address=[tcp|udp]://[ip]:[port]

		Ex:
		docker run -d --log-driver=syslog --log-opt syslog-address=tcp://192.168.10.21:514 docker.io/busybox /bin/sh -c "while true ; do date ; sleep 1;  done"
		
		远程主机配置
		[root@localhost ~]# vi /etc/rsyslog.conf 
		$ModLoad imtcp
		$InputTCPServerRun 514

		[root@localhost ~]# systemctl  restart rsyslog
		[root@localhost ~]# tail /var/log/messages  -f
		
	Fluentd日志:
		Fluentd可用收到收集并转发日志，经常与一些经典工具组合使用
			客户端应用>Fluentd>elasticsearch>kibana
			Fluentd+elasticsearch+kibana

			docker load -i elasticsearch.tar
			docker load -i fluentd-es.tar 
			docker load -i kibana.tar
		
		Fluentd日志格式
			docker run -d -p 9200:9200 --name elasticsearch docker.io/elasticsearch
			docker run -d -p 5601:5601 --name kibana --link elasticsearch:elasticsearch docker.io/kibana
			mkdir /fluentd/conf
			vi /fluentd/conf/fluent.conf
			
		
		运行:
		docker run -d -p 24224:24224 -p 24224:24224/udp -v /fluentd/conf:/fluentd/etc --name fluentd --link elasticsearch:elasticsearch docker.io/fluent/fluentd-es

		运行客户端测试
		docker run -d --log-driver=fluentd --log-opt fluentd-address=localhost:24224 --log-opt tag="busybox" --link fluentd:fluentd  docker.io/busybox  /bin/sh -c "while true ; do date ; sleep 1 ; done”

		打开浏览器访问
		http://localhost:5601
		
		
		docker run -d -p 24224:24224 -p 24224:24224/udp -v /fluentd/conf:/fluentd/etc --name fluentd --link elasticsearch:elasticsearch docker.io/fluent/fluentd-es
		docker run -d --log-driver=fluentd --log-opt fluentd-address=localhost:24224 --log-opt tag="busybox" --link fluentd:fluentd  docker.io/busybox  /bin/sh -c "while true ; do date ; sleep 1 ; done"
		
Compose定义运行复杂一系列docker命令

	安装
	curl -L https://github.com/docker/compose/releases/download/1.20.0-rc2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
	chmod +x /usr/local/bin/docker-compose
	docker-compose -v
	
docker-compose基本参数
	--version 查看版本
	-f 使用非docker-compose.yml命名的yaml文件
	up -d后台启动服务
	start/stop 停止/启动docker-compose容器
	rm 删除docker-compose容器
	ps 查看docker-compose正在运行的容器
	相关语法 请参考
	https://docs.docker.com/compose/compose-file/compose-file-v2/#compose-and-docker-compatibility-matrix
	
--------------------------	
	
	容器管理平台
	业内建议使用Kubernetes
	https://www.kubernetes.org.cn/3546.html
	https://www.kubernetes.org.cn/doc-25

	HAProxy与Nginx
	https://www.jianshu.com/p/d436a3e73d2f

	培训老师github
	https://github.com/kevinzwf
	https://github.com/testalauda

	安装:
	docker pull jenkins
	docker run -p 8080:8080 -p 5000:5000 -d -v /var/jenkins_home jenkins
```

