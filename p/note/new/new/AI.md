# AI（人工智能）

**AI包含机器学习**

AI > 机器学习 > 深度学习 > cv,nlp,vc

参考：

```
小白篇：一文搞懂AI是如何学习的？
https://baijiahao.baidu.com/s?id=1762630345819622814&wfr=spider&for=pc

人工智能，机器学习，推荐书籍
https://item.jd.com/12732035.html#none

深度学习四大框架之争(Tensorflow、Pytorch、Keras和Paddle)
https://baijiahao.baidu.com/s?id=1733159772977730209&wfr=spider&for=pc

【整整600集】北大教授196小时讲完的AI人工智能从入门到项目实战全套教程，全程干货无废话！
https://www.bilibili.com/video/BV14a4y1V7YJ
```

###### “深度学习”，“神经网络”，“线性回归”与数学的关系：“深度学习”是用多层“神经网络”进行特征转换和模式识别的算法，“神经网络”就是“线性回归”，“线性回归”基础就是“线性代数”，所以学习人工智能基础数学是“线性代数”

###### 神经网络（模拟网站）：http://playground.tensorflow.org/
## 一。机器学习

### 1.机器学习分类：

机器学习是人工智能的一个子领域，它使计算机能够从数据中学习并做出决策或预测，而不是依赖于严格的编程指令。机器学习可以分为多种类型，主要基于学习方式和学习目标的不同。以下是一些常见的机器学习分类：

##### 根据学习方式分类：

1. **监督学习（Supervised Learning）**:
   - 定义：使用带有标签的数据集进行训练，模型学习将输入映射到输出。
   - 应用：分类（如邮件过滤）、回归（如房价预测）。
2. **无监督学习（Unsupervised Learning）**:
   - 定义：使用没有标签的数据集进行训练，模型尝试自己发现数据中的结构。
   - 应用：聚类（如市场细分）、关联规则学习（如购物篮分析）。
3. **半监督学习（Semi-supervised Learning）**:
   - 定义：训练数据由少量带标签和大量不带标签的样本组成。
   - 应用：可以用于减少对大量标注数据的依赖。
4. **强化学习（Reinforcement Learning）**:
   - 定义：模型（智能体）通过与环境交互来学习最佳策略。
   - 应用：游戏、机器人导航、资源管理。

##### 根据学习目标分类：

1. **分类（Classification）**:
   - 目标：将输入数据分配到一个或多个预先定义的类别中。
   - 示例：垃圾邮件检测、图像识别。
2. **回归（Regression）**:
   - 目标：预测一个连续的数值。
   - 示例：房价预测、股票价格预测。
3. **聚类（Clustering）**:
   - 目标：基于数据相似性将数据分组。
   - 示例：市场细分、社交网络分析。
4. **降维（Dimensionality Reduction）**:
   - 目标：减少数据集中的变量数量，同时保持大部分信息。
   - 示例：特征提取、数据压缩。
5. **异常检测（Anomaly Detection）**:
   - 目标：识别不符合预期模式的数据点。
   - 示例：信用卡欺诈检测、工业故障检测。
6. **推荐系统（Recommendation Systems）**:
   - 目标：预测用户可能感兴趣的项目或服务。
   - 示例：电影推荐、产品推荐。

##### 其他分类：

1. **在线学习（Online Learning）**:
   - 特点：模型通过不断学习新数据来更新自己。
2. **批量学习（Batch Learning）**:
   - 特点：模型在特定时间点使用所有可用数据进行训练。
3. **基于实例的学习（Instance-based Learning）**:
   - 特点：模型通过记住训练实例来进行预测。
4. **模型基础的学习（Model-based Learning）**:
   - 特点：模型使用训练数据来构建一个代表数据的简化模型。

这些分类并不是互斥的，许多机器学习任务和算法可以跨越不同的类别。例如，深度学习是一种特殊类型的机器学习，它使用多层神经网络来学习数据的复杂模式，可以应用于监督学习、无监督学习和强化学习的任务中。随着技术的发展，机器学习的分类和应用也在不断扩展。



## 二。大模型

### 1.大模型使用

##### 1.1.chatgpt的注册与使用

```
1.1）首先登录openai官网（需要魔法）
登录前需要魔法，魔法后的ip要为印度，美国等国家ip

1.2）推荐使用国外邮箱注册openai账号，例如google账号
需要有一个国外手机号接收验证码，使用如下网站提供虚拟号服务来接收验证码
https://sms-activate.org/

1.3）使用googole账号注册openai账号时输入虚拟号等待验证码
收到验证码后进行注册
```

chatgpt API调用

```
搭建chatgpt服务
https://www.bilibili.com/video/BV1vg4y1L7wi
chatgpt调用api
https://www.bilibili.com/video/BV13h411j7Ti
```



## 三。部署大模型

#### 3.1.概要



#### 3.2.部署chatglm

硬件确认，GPU显存达到要求，推荐在ubuntu

##### * ubuntu部署

1）ubuntu软件源更新及必要工具vim安装（linux目录推荐为英文）

$ sudo apt update	

$ sudo apt upgrade

$ 安装goole chrome浏览器

$ 配置科学上网(vpn)

配置vpn原因是后面需要下载chatglm3-6B模型权重，可以访问google网站



2）安装显卡驱动

Step 1两种方式：

​			方式1：使用官方的NVIDIA驱动进行手动安装，这种方式比较稳定，靠谱，但可能会遇到很多问题

​			方式2：使用系统自带的“软件和更新”程序-附加驱动更新，这种方法要联网，但非常简单，很难出现问题（推荐这种方法）

```
sudo apt install gcc
sudo apt install g++
sudo apt install make
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev
libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libopenblas-dev liblapack-dev libatlas-base-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
```

Step 2 禁用ubuntu默认的显卡驱动：

​	$ vi /etc/modprobe.d/blacklist.conf

​	文件最后面添加一行如下：

​	blacklist nouveau 	

Step 3 让配置立即生效 ：

​	$ sudo update-initramfs -u

Step 4 打开系统“software Updater”

​	 保持联网状态，点击Apply changes，重启



**可以进入NVIDIA官网确认本机要下载的驱动版本（手动安装方式）：**

https://www.nvidia.cn/Download/index.aspx?lang=cn

选择好显卡驱动和适用平台后，点击下载。



下载完成后，对该驱动添加执行权限，否则无法进入安装页面。
在安装之前，需要关闭图形化界面，需要判断你目前的ubuntu系统的图像化界面管理器是
gdm3（默认）或是其它。gdm3 或 lightdm负责登录界面和用户会话的初始化，是系统启动进程的一
部分，用于用户登录和启动图形用户界面 (GUI) 会话。其中gdm3是安装Ubuntu系统时默认安装的，而
lightdm可以选择性安装，它是一个更轻量级的显示管理器。



关闭的原因是因为显示管理器（如 gdm3、lightdm）控制着图形界面，包括使用显卡驱动来显示
内容。在这些图形界面运行时尝试安装或更新显卡驱动可能会导致冲突，因为驱动程序文件可能正在被
系统使用。所以我们需要进入命令行模式来安装显卡驱动。



如果之前执行过 sudo apt install lightdm ，就说明当前环境下已经使用lightdm代替了gdm3，此时需要如下命令关闭：

$ sudo service lightdm stop

否则就是默认的gdm3,这样关闭

$ sudo /etc/init.d/gdm3 stop

关闭后，进入命令行模式，最简单的方法是使用telinit命令更改为运行级别3。执行如下linux命令后显示服务器将停止。

$ bash sudo telinit 3

通过Ctrl + Alt + F3(f1-f6)快捷打开终端，先登录后输入如下命令：

\# 删除已安装的显卡驱动
$ sudo apt-get remove --purge nvidia*
$ cd Downloads
$ sudo ./NVIDIA-Linux-x86_64-430.26.run –no-opengl-files –no-x-check

随后进入安装界面，依次选择“continue”-->不安装32位兼容库（选择no）-->不运行x配置（选择no）即可。

最后输入“reboot”命令重启机器。重新进入图形化界面，在终端输入nvidia-smi命令即可

**验证显卡安装成功**：

$ nvidia-smi



3）安装cuda(并行计算框架)

CUDA 提供了两种主要的编程接口：CUDA Runtime API 和 CUDA Driver API。

* CUDA Runtime API 是一种更高级别的抽象，旨在简化编程过程，它自动处理很多底层细节。大多
  数 CUDA 程序员使用 Runtime API，因为它更易于使用。
* CUDA Driver API 提供了更细粒度的控制，允许直接与 CUDA 驱动交互。它通常用于需要精细控
  制的高级应用。

而要安装CUDA，其实就是在安装CUDA Toolkit， 其版本决定了我们可以使用的 CUDA Runtime
API 和 CUDA Driver API 的版本，当安装 CUDA Toolkit 时会安装一系列工具和库，用于开发和运行
CUDA 加速的应用程序。这包括了 CUDA 编译器（nvcc）、CUDA 库和 API，以及其他用于支持 CUDA
编程的工具。如果安装好 CUDA Toolkit，就可以开发和运行使用 CUDA 的程序了。



验证CUDA安装：

当我们运行 CUDA 应用程序时，通常是在使用与安装的 CUDA Toolkit 版本相对应的 Runtime
API。这可以通过 nvcc -V 命令查询

$ nvcc -V



**CUDA安装方式：**

​		方式一：

​					$ apt install nvidia-cuda-toolkit 

​		方式二：

​					需要进入NVIDIA官网：https://developer.nvidia.com/cuda-toolkit-archive 

​					找到需要下载的Cuda版本。



但其实，**通常不需要预先手动安装 CUDA** ，因为我们目前使用的 PyTorch 等框架在安装过程会处
理这些依赖。当我们通过 Conda/pip等方式安装 PyTorch 时会指定的 CUDA 版本，该 CUDA 版本就会
与当前的Pytorch版本相兼容，预编译并打包了与 CUDA 版本相对应的二进制文件和库。所以除非有特
定的需求或要进行 CUDA 级别的开发，才可能需要手动安装 CUDA Toolkit。



4）python环境（安装Anaconda环境）

Anaconda的作用：

Anaconda是一个为科学计算设计的发行版，适用于数据科学、机器学习、科学计算和工程领域。
它会提供大量预安装的科学计算和数据科学相关的库，且提供了 Conda 这样一个包管理器，用来安
装、管理和升级包，同时也可以创建隔离的环境以避免版本和依赖冲突。相较于单独安装Python，对初
学者更友好，尤其是对于不熟悉 Python 和包管理的用户。

运行大模型需要 Python 环境。所以我们这里选择使用Anaconda来构造和管理Python环境。



Anaconda安装：

​			进入Anaconda官网下载安装包：https://www.anaconda.com/download

Step1 安装Anaconda：

​		$ bash Anaconda3-2023.09.0-Linux-x86_64.sh

​		在此处输入“yes”,然后按“Enter”键使用Anaconda的默认安装位置（/home/${account}/anaconda3）

Step 2 配置环境变量:

​		$ vi .bashrc

​		#我的anaconda3的安装路径是/home/muyu/anaconda3
​		export PATH=/home/liyuan/anaconda3/bin:$PATH

​	    $ source .bashrc

Step 3 启动anaconda

​		$ anaconda-navigator



安装python方式管理：

。。。。。。。。。。。。。。。



5）运行chatglm3-6b

。。。。。。



##### * windows部署

查看GPU显存命令：dxdiag



1）安装显卡驱动

在左面鼠标右键看有没有NVIDIA控制面板，如果没有根据ubuntu章节到官网下载显卡驱动并安装



2）安装cuda

根据ubuntu章节



3）配置项目运行环境

Anaconda方式（安装路径建议不要改）：

​					验证：

​					启动管理工具，点击“CMD.EXE”输入命令 ：conda --version



python方式：

​					



4）本地部署chatglm3-6b(下载文件)

* chatglm3-6b项目文件

  https://github.com/THUDM/ChatGLM3

* 下载权重文件

  





##### * 多卡启动chatglm3-6b



