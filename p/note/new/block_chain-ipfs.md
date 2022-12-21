# ipfs

```
官网(ipfs下一代http)
http://ipfs.io

官网访问host配置
	209.94.90.1 ipfs.io
	209.94.90.1 docs.ipfs.io
	209.94.90.1 dist.ipfs.io

下载地址
https://github.com/ipfs/go-ipfs/releases
https://github.com/ipfs/ipfs - IPFS 规范
https://github.com/ipfs/go-ipfs - Go 实现
https://github.com/protocol/ipfs-webui - Web 工作台

搭建私有网络
	https://blog.csdn.net/oscube/article/details/80598790
		
		两台机器搭建测试
		192.168.5.11
		192.168.5.12
	
	1.分别初始化
		ipfs init

	2.搭建私服生成秘钥
		https://github.com/Kubuxu/go-ipfs-swarm-key-gen	
		秘钥项目
		https://github.com/Kubuxu/go-ipfs-swarm-key-gen
		下载(在目录/root/go/src/github.com/Kubuxu/go-ipfs-swarm-key-gen/ipfs-swarm-key-gen)
		go get -u github.com/Kubuxu/go-ipfs-swarm-key-gen/ipfs-swarm-key-gen	//或者clone项目
		cd /root/go/src/github.com/Kubuxu/go-ipfs-swarm-key-gen/ipfs-swarm-key-gen
		go build		//当前文件生成ipfs-swarm-key-gen
		./ipfs-swarm-key-gen > swarm.key	//copy到.ipfs目录下面
	
	3.分别copy上面生成的swarm.key到11,12机器的.ipfs目录下面
	
	4.分别执行清除
	ipfs bootstrap rm --all
	
	5.启动
	192.168.5.11 //启动11
	ipfs daemon
	
	192.168.5.12
	ipfs bootstrap add /ip4/192.168.5.11/tcp/4001/ipfs/QmQeSGpii4PWu18K4mVNiNqCSNF7HPWGdKNSp9nsRHHNrE	//hash为11节点id
	ipfs daemon	//启动12
	
	6.验证
		在11机器 ipfs add file
		可在12机器查看 ipfs cat hash
	
	查看连接节点
	ipfs swarm peers

其他机器也能访问
	https://my.oschina.net/u/2601303/blog/1817410
	vi config
	修改配置文件的127.0.0.1为0.0.0.0就行了
	"API": "/ip4/0.0.0.0/tcp/5001",
    "Gateway": "/ip4/0.0.0.0/tcp/8080"

ipfs跨域问题

	
IPFS实践之初体验
http://www.cnblogs.com/kekbin/p/9253288.html

浏览器中使用
https://blog.csdn.net/shebao3333/article/details/80009120

文件共享命令ipfs publish
https://blog.csdn.net/chenmo187J3X1/article/details/80988140

常用命令
http://cw.hubwiz.com/card/c/ipfs/1/1/1/

可视化工具
https://baijiahao.baidu.com/s?id=1615427234228505599&wfr=spider&for=pc

网络上分享文件
https://baijiahao.baidu.com/s?id=1596786086151355833&wfr=spider&for=pc
https://bihu.com/article/138549
----------------------------------------------------------------------------------------------------
安装
tar -xzvf go-ipfs_v0.4.18_linux-amd64.tar.gz	//解压
./install.sh	//copy ipfs 文件到/usr/local/bin目录

初始化(会在当前用户目录生成.ipfs目录)
ipfs init

节点身份标识
ipfs id

启动deamon
ipfs daemon
web访问
http://localhost:5001/webui

上传文件
ipfs add <filePath> //添加文件
ipfs add -r <dirPath> //上传文件夹


下载文件
ipfs get <hash> -o <output_path>
查看命令
ipfs cat <hash>
长期保留文件到本地
ipfs pin add <hash>
ipfs pin ls //查看哪些文件在本地是持久化的。
ipfs pin rm QmQnk1LcnsJJfZNF8z7yFiYDH3JFmu4vciebHmZQxVZz4C

分享文件
官网
http://ipfs.io/ipfs/<file hash>

发布网站

公网访问
	ipfs add file//添加文件,获取hash
	ipfs daemon //启动服务
	https://ipfs.io/ipfs/hash //如果要用此网关访问,必须启动本地服务daemon,然后curl或者wget请求一次


	测试文件
	//菊花
	https://ipfs.io/ipfs/QmQnk1LcnsJJfZNF8z7yFiYDH3JFmu4vciebHmZQxVZz4C
	//沙漠
	https://ipfs.io/ipfs/QmaU4RP1YoAGMrdmGateeTWSTAPZ6ryF466rFV9HVJdZg1
	//txt
	curl http://ipfs.io/ipfs/QmPgKZH5zQtL8ht8vite5MgNc6ytKYim4zvPV5fYh7DZXP
	//png
	curl http://ipfs.io/ipfs/QmQT6yRQiQUS1uuNuYqJMzTDAgFznLPJYguDa2S8LfMCAw

	//测试视频
	明说(01)：从0开始搭建区块链开发环境-_超清.mp4
	https://ipfs.io/ipfs/QmVUeBzsomwXiVSr2N6cYkYhgD1UZtibjDjHPhxfprd5Ss
	
	打印系统流程
	http://ipfs.io/ipfs/Qme3Kuoq1Tn21Pa286RgSZDp9xp1DktFFSFBp7LKFvYNtT
	
	创建博客
	http://www.sohu.com/a/205907177_100000851
----------------------------------------------------------------------------------------------------
建博客
https://blog.csdn.net/lzl001/article/details/81904390

一.添加目录
ipfs add -r demo
 3.44 KiB / ? [-=------------------------------------------------------------] 
added QmUgGQg892RWbVdpzR8er5SJr4jfs3YrWRth7iA4tSs2ff demo/changel.png
 3.61 KiB / 3.61 KiB [================================================] 100.00%
added QmUxvuLD31wxtBaetPybWbWZn7gp7Q1834Eoc5zoQsjCYV demo/index.html
 3.61 KiB / 3.61 KiB [================================================] 100.00%
added Qma8Yd2YB7r85M1u34YLF5epyfY5BNEmZeeUHnRCjLHqQF demo
 3.61 KiB / 3.61 KiB [================================================] 100.00%
  
http://ipfs.io/ipfs/Qma8Yd2YB7r85M1u34YLF5epyfY5BNEmZeeUHnRCjLHqQF
http://127.0.0.1:8080/ipfs/Qma8Yd2YB7r85M1u34YLF5epyfY5BNEmZeeUHnRCjLHqQF

二.发布节点(demo目录的hash)
ipfs name publish Qma8Yd2YB7r85M1u34YLF5epyfY5BNEmZeeUHnRCjLHqQF
D:\>ipfs name publish Qma8Yd2YB7r85M1u34YLF5epyfY5BNEmZeeUHnRCjLHqQF
Published to QmU5ZFK582uUsUrQxf4uL7fpXHoR1FoE52K2V1qz6qapSv: /ipfs/Qma8Yd2YB7r85
M1u34YLF5epyfY5BNEmZeeUHnRCjLHqQF

访问
通过节点id访问:http://ipfs.io/ipns/peerid
http://ipfs.io/ipns/QmU5ZFK582uUsUrQxf4uL7fpXHoR1FoE52K2V1qz6qapSv
查看节点Id: ipfs id 
QmU5ZFK582uUsUrQxf4uL7fpXHoR1FoE52K2V1qz6qapSv就是本机Id
通过publish目录hash,来发布节点
ipfs name publish Qma8Yd2YB7r85M1u34YLF5epyfY5BNEmZeeUHnRCjLHqQF


修改后重新publish目录hash

http://ipfs.io/ipns/QmU5ZFK582uUsUrQxf4uL7fpXHoR1FoE52K2V1qz6qapSv






ipfs
https://ipfs.io/ipfs/QmUgGQg892RWbVdpzR8er5SJr4jfs3YrWRth7iA4tSs2ff
```



# block_chain

```
视频教程:
http://wangxiaoming.com/
http://i.youku.com/i/UMzUxMzkxMDc2/videos?spm=a2hzp.8244740.0.0
bilibili
https://www.bilibili.com/video/av27691535/
https://www.bilibili.com/video/av63056406/?redirectFrom=h5
https://www.iqiyi.com/v_19rrbfi8h0.html#curid=960987400_8083d013af03caed6ea68d30666e14e1

官网:
https://www.ethereum.org/
https://www.ethereum.org/cli
https://geth.ethereum.org

云盘:
liyuandf@共享资源/组件技术/区块链开发视频

ubuntu下安装:
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum

1.连接主网络
geth --datadir data --networkid 1

区块链数据:
--datadir data //当前data目录(默认$HOME/.ethereum)

创建私有网络:
--networkid 给个随机id(默认为1)

--dev	//运行一个私有网络(提供多个日志和调试相关的标记)

2.创建账户
geth account new	//输入密码加密账户(忘记密码无法访问账户)
Passphrase:	//123
Repeat passphrase: //123
Address1: {1fd1658f7283d24035a5973ca20b34cde19dab3e}

Address2: {9aae7bdb6e0343c57edcda3aedd0d261f8123100}

geth account list	//获取本地所有账户列表

3.挖矿

GPU列表
geth gpuinfo

geth --mine --minerthreads 16 --minergpus '0,1,2' --etherbase '1fd1658f7283d24035a5973ca20b34cde19dab3e'
--unlock '1fd1658f7283d24035a5973ca20b34cde19dab3e' //解锁账户

参数解释
--minerthreads 16 											//线程总数
--minergpus '0,1,2' 										//指定使用的GPU
--etherbase '1fd1658f7283d24035a5973ca20b34cde19dab3e'		//赚取回报存入的地址


4.快速同步
fast sync

-----------------------------------视频start------------------------------------
查看命令文档
geth -h

列出账户列表
geth account list

启动
geth --datadir /home/liyuan/block/dev_data --dev
启动控制台
geth --dev console 2>> file_to_log_output

查看有没有账户
eth.accounts

创建账户
personal.newAccount('123456')

账户赋值
user1=eth.accounts[0]//user2
user1
user2=eth.accounts[1]//user3
user2

查看账户余额
eth.getBalance(user1)//user2
eth.getBalance(user2)//user3

查看区块号
eth.blockNumber

挖矿前把日志打开
tail -f file_to_log_output

挖矿默认充值到第一个账户
解锁第一个账户
personal.unlockAccount("0x67c901e249aa49ef09912fb58cd80320582f5cf9","")
personal.unlockAccount("0x67c901e249aa49ef09912fb58cd80320582f5cf9","123456")//
给第二个账户充值3个以太币
eth.sendTransaction({from: user1, to:user2,value: web3.toWei(3,"ether")})
eth.getBalance(user2)//查看以太币发现还是为0,因为要启动挖矿

启动挖矿
miner.start()

eth.getBalance(user2)//查看第二个账户发现已经有3个以太币
eth.blockNumber//查看区块号有"1"个区块


问题:
1.启动miner.start()为null(新版本中返回Null,后台在监听交易)
2.(geth account list)默认有一个账户?查看不到其他账户,账户不匹配问题
-----------------------------------------------------------------------
生产环境使用go,c++客户端
truffle 是solidity编程语言开发框架
ethereumjs-testrpc 开发以太坊客户端(基于本机内存,开发比较快)
npm需要安装nodejs

安装nodejs
sudo apt install npm

安装truffle
sudo npm install -g truffle
	 npm install -g truffle@2.0.8
问题
1.gyp: Call to 'node -e "require('nan')"' returned exit status 127 while in binding.gyp
解决如下
sudo apt-get update && sudo apt-get install nodejs-legacy

2./usr/local/lib/node_modules/truffle/build/cli.bundled.js:33819
nodejs 版本过低
https://blog.csdn.net/well2049/article/details/79138045

sudo npm config set registry https://registry.npm.taobao.org 	//更换淘宝的镜像
sudo npm config list	//查看配置是否生效
sudo npm install n -g	//安装更新版本的工具N
sudo n stable		//跟新node版本

安装ethereum客户端
sudo npm install -g ethereumjs-testrpc
	 npm install -g ethereumjs-testrpc@3.0.5
//uglifyjs-webpack-plugin

新建一个项目
mkdir demo1
cd demo1
truffle init

编译项目
truffle compile
 
部署项目
testrpc	//部署前先启动
truffle deploy	//truffle2.0以上版本变成truffle migrate

启动服务
truffle serve

浏览器访问
http://127.0.0.1:8080


项目目录结构：
app//前端界面
build//部署编译好的文件
contracts//智能合约文件
migrations//部署的脚本
test//测试
-------------------------------------视频end----------------------------------
入门体验
http://baijiahao.baidu.com/s?id=1587654252975914463&wfr=spider&for=pc

1.新建数据目录data0
mkdir data0

2.新建vi genesis.json
{
  "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "coinbase" : "0x0000000000000000000000000000000000000000",
    "difficulty" : "0x40000",
    "extraData" : "",
    "gasLimit" : "0xffffffff",
    "nonce" : "0x0000000000000042",
    "mixhash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "timestamp" : "0x00",
    "alloc": { }
}

3.初始化
geth --datadir data0 init genesis.json

4.启动节点
geth --datadir ./data0 --networkid 11 console

进入测试网的交互控制台

查询账户
eth.accounts


新建账户
personal.newAccount("123")

查看账户余额
eth.getBalance(eth.accounts[0])

启动/停止挖矿
miner.start()
miner.stop()




以太坊基本概念
https://www.jianshu.com/p/c5befc8dd1ec


编译发布,调用
https://blog.csdn.net/CHENYUFENG1991/article/details/53458175

geth老的版本已移除这个方法
eth.getCompilers()
Error: The method eth_getCompilers does not exist/is not available


1.启动合约
geth --datadir data0 --networkid 11 console


mist 与 Ethereum-Wallet
Mist是一个去中心化应用浏览器
Ethereum-Wallet 是以太坊钱包,紧紧是钱包的一种
https://bitshuo.com/topic/5840f5b463baf1df6cad0d3b

https://github.com/ethereum/mist

-----------------------------------------------------------------------
编写智能合约Solidity

安装solc编译器
https://blog.csdn.net/HiBlock/article/details/79720286

https://github.com/ethereum/browser-solidity

Solcjs:允许在node.js中以编程方式编译Solidity

Browser solidity : 适用于小型合约的IDE集成开发环境
	提供一个编辑器,并生成代码部署合约
	

区块链学习视频
http://yuntv.letv.com/bcloud.html?uu=obyao0lchj&vu=c0adea3148&auto_play=0&width=640&height=360&lang=zh_CN

book addr
http://www.packtpub.com/
http://www.hzbook.com

-----------------------------------------------------------------------

区块链开发视频
http://i.youku.com/i/UMzUxMzkxMDc2/videos?spm=a2hzp.8244740.0.0

区块链视频
http://v-wb.youku.com/v_show/id_XMTcxODczOTMxNg==.html

https://blog.csdn.net/wjx342529/article/details/70148987

geth --datadir /home/liyuan/block/dev_data --dev
geth --datadir /home/liyuan/block/data --networkid 1

personal.unlockAccount
```

