# openresty

官网：http://openresty.org/cn/

官网文档：https://www.nginx.com/resources/wiki/modules/lua/

### mac桌面docker开发环境：

​			苹果mac运行docker,没有桥网卡，宿主机不能直接ping通容器ip，所以要用"-p"参数（-p 80:80）来把端口映射到宿主机上（-v容器挂载目录要改成苹果的data目录）

```
# mycentos_openrestry
docker run --name myOpenrestry --net dev-net --ip 172.18.1.20 -p 80:80 --privileged=true -it -d registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/mycentos:sshnopwd
# mycentos_nginx
docker run --name myOpenrestry2 --net dev-net --ip 172.18.1.21 -p 81:80 --privileged=true -it -d registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/mycentos:sshnopwd
# mycentos
docker run --name myOpenrestry3 --net dev-net --ip 172.18.1.22 --privileged=true -it -d registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/mycentos:sshnopwd
```
### 一。安装

```
# add the yum repo:
wget https://openresty.org/package/centos/openresty.repo
sudo mv openresty.repo /etc/yum.repos.d/

# update the yum index:
sudo yum check-update

# 然后就可以像下面这样安装软件包，比如 openresty：
sudo yum install -y openresty

# 如果你想安装命令行工具 resty，那么可以像下面这样安装 openresty-resty 包：
sudo yum install -y openresty-resty

# 列出所有 openresty 仓库里头的软件包：
sudo yum --disablerepo="*" --enablerepo="openresty" list available
```

### 二。启动

配置环境变量path(略掉)：/usr/local/openresty/nginx/sbin

```
安装目录：
$ cd /usr/local/openresty/nginx

启动（没配置环境变量启动方式）：
$ ./sbin/nginx -c conf/nginx.conf

停止（没配置环境变量启动方式）：
$ ./sbin/nginx -c conf/nginx.conf -s stop

杀进程：
$ netstat -lntp
$ kill {80端口进程}

访问：
http://127.0.0.1
```
### 1.hello world

```
1.配置nginx.conf:
location /hello {
     default_type text/html;
     content_by_lua_block {
        ngx.say("<p>hello, world</p>")
     }
}

访问：
http://127.0.0.1/hello

文件形式,新建lua目录（/usr/local/openresty/nginx/lua）：
location /hello {
     default_type text/html;
     content_by_lua_file lua/hello.lua;
}
hello.lua内容：
ngx.say("hello, world,文件形式")

缓存默认开启（配置在server下面）：
lua_code_cache on	//默认模式
lua_code_cache off	//关闭，改变lua脚本后不需要重启nginx
```
### 2.校验参数，并返回json格式

```
nginx配置
location /decode_info {
   content_by_lua_file lua/decode_info.lua;
}

需求：
http请求decode_info，校验参数（如果为空直接返回）
拿到参数后，放上ip,以json数据返回前端

lua代码：
local json = require "cjson"	--openresty安装后自带的joson库

ngx.req.read_body()		--ngx的API
local args = ngx.req.get_post_args()

if not args or not args.info then
   ngx.exit(ngx.HTTP_BAD_REQUEST)
end

local client_ip = ngx.var.remote_addr	--nginx的变量
local user_agent = ngx.req.get_headers()['user-agent'] or ''
local info = ngx.decode_base64(args.info)

local response = {}
response.info = info
response.ip = client_ip
response.user_agent = user_agent
ngx.say(json.encode(response))

访问：
curl -i --data 'info=abc123' http://127.0.0.1/decode_info

需要注意的：
1.要调用openresty的API,不要调用lua自身的API,否则会阻塞
```