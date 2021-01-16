# k8s核心
测试集群命令：

```
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
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

### 三．controller介绍

### 四.service介绍

### 五．配置管理secret,configMap

### 六．集群安全机制RBAC

### 七．Ingress

### 八．Helm

### 九．持久化存储





