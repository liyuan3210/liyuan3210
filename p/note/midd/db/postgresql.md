# postgresql

### 一。postgresql容器安装

```
https://www.cnblogs.com/mingfan/p/12332506.html

1.查找镜像
docker sreach postgresql
2.查找tag
https://registry.hub.docker.com/
3.拉取镜像
docker pull postgres:11.12
4.运行服务
docker run --name postgres2 -e POSTGRES_PASSWORD=888888 -e TZ=PRC -p 5432:5432 -v /data/pgdata:/var/lib/postgresql/data -d postgres:11.12
-e POSTGRES_PASSWORD 密码（默认用户名postgres）
-e TZ=PRC 时区中国(如果在启动容器时不设置时区，默认为UTC，使用now()设置默认值的时候将有时间差)
```

### 二。pgadmin4安装

```
1.拉取(查找版本)
docker pull dpage/pgadmin4:5

2.运行服务
docker run -d -p 81:80 --name pgadmin44 -e PGADMIN_DEFAULT_EMAIL=465049568@qq.com -e PGADMIN_DEFAULT_PASSWORD=999999 dpage/pgadmin4:5

3.创建数据库连接
使用docker IP访问

创建表方法
https://www.jianshu.com/p/e7fe45a10cee
https://blog.csdn.net/qq_28289405/article/details/80243476
```

### 三。postgresql递归查询

```
创建表：
CREATE TABLE public.subject (
	subject_uuid varchar NULL,
	name varchar NULL,
	sfather_uuid varchar NULL,
	sts varchar NULL
);

-- Column comments

COMMENT ON COLUMN public.subject.subject_uuid IS '栏目id';
COMMENT ON COLUMN public.subject.name IS '栏目名称';
COMMENT ON COLUMN public.subject.sfather_uuid IS '父uuid';
COMMENT ON COLUMN public.subject.sts IS '状态0：正常 ，1：冻结';


插入数据：
INSERT INTO public.subject (subject_uuid,"name",sfather_uuid,sts) VALUES
	 ('1','bbb','0','0'),
	 ('2','ccc','1','0'),
	 ('3','ddd','1','0'),
	 ('4','eee','2','0');
	 
递归sql:
with recursive r as (
select s.subject_uuid ,s.sts from subject s where s.sts = '0' and s.subject_uuid = '1'
union all 
select s2.subject_uuid ,s2.sts from subject s2,r where s2.sts = '0' and s2.sfather_uuid = r.subject_uuid
)
select r.subject_uuid from r where sts = '0'
```

