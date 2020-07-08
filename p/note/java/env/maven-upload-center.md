# 上传本地/中央仓库

##  1.上传到本地仓库

```
1.settings.xml配置用户名和密码(上传内部releases版本,内部/个人快照版本,根据使用情况应该分两个账号)
	一定要是C:\Users\用户名\.m2\settings.xml下面的settings.xml
	<server>  
		<id>releases</id>  
		<username>admin</username>  
		<password>admin123</password>  
	</server>
	<server>  
		<id>snapshots</id>  
		<username>admin</username>  
		<password>admin123</password>  
	</server>

	2.pom.xml文件配置(id对应上面配置的账号,url根据nexus仓库地址配置)
	maven2会根据模块的版本号(pom文件中的version)中是否带有-SNAPSHOT来判断是快照版本还是正式版本
	<distributionManagement>
		<repository>  
			<id>releases</id>  
			<url>http://192.168.5.15:8081/repository/maven-public/</url>  
		</repository>
		<snapshotRepository>
			<id>snapshots</id>
			<url>http://192.168.5.15:8081/repository/maven-snapshots/</url>
		</snapshotRepository>
	</distributionManagement>

	打包测试验证
	mvn clean install
	
	deploy(发布)
	运行命令mvn -Dmaven.test.skip=true deploy
	mvn clean deploy -P release -Dmaven.test.skip=true


单个jar上传(貌似不行)
	mvn deploy:deploy-file -DgroupId=com -DartifactId=eureka_server -Dversion=0.0.1 -Dpackaging=jar -Dfile=D:/eureka_server-0.0.1-releases.jar -Durl=http://192.168.5.15:8081/repository/liyuan_hosted/ -DrepositoryId=liyuan_hosted

指定本地目录(settings.xml)
	settings节点下面
	<localRepository>D:\soft\dev\mavenRepository</localRepository>

指定私有仓库settings.xml(指定从私服下载,下载后jar的会在私服缓存)
	settings>mirrors节点下面
	<mirror>
		<id>nexus</id>
		<mirrorOf>*</mirrorOf>
		<url>http://10.1.193.100:8081/nexus/content/groups/public/</url>
	</mirror>
```

## 2.上传到中央仓库

```
实例
	https://www.sojson.com/blog/250.html
	
	默认中央仓库
	https://www.cnblogs.com/EasonJim/p/6784328.html
	
	Hudson插件持续集成
	
	上传到中央仓库

申请中央仓库	
	· maven中央仓库
	https://issues.sonatype.org/
	sonatype帐号登录
	申请流程
	1.创建好账号登录进去后,点击create创建一个工单
	project:选择Community Support-Open...
	Issue Type:New Project
	Group id:现有域名com.liyuan3210 或 (io.github.liyuan3210 或 com.github.liyuan3210)
	Project URL: https://github.com/liyuan3210/base_java
	SCM url: https://github.com/liyuan3210/base_java.git
	
	2.创建后Group id 分两种情况
	  自己域名
	   通过邮件提示配置TXT记录 如
		@			OSSRH-51896
		配置好后坐等成功邮件通知(通常)
	  github地址
	     需要根据邮件创建一个OSSRH-51896仓库
		 然后坐等成功邮件通知
	
申请成功邮件回复如下
	com.github.liyuan3210 has been prepared, now user(s) liyuan3210 can:
	* Deploy snapshot artifacts into repository https://oss.sonatype.org/content/repositories/snapshots
	* Deploy release artifacts into the staging repository https://oss.sonatype.org/service/local/staging/deploy/maven2
	* Promote staged artifacts into repository 'Releases'
	* Download snapshot and release artifacts from group https://oss.sonatype.org/content/groups/public
	* Download snapshot, release and staged artifacts from staging group https://oss.sonatype.org/content/groups/staging

	please comment on this ticket when you promoted your first release, thanks
	
	· 仓库查看地址(sonatype帐号登录)
	https://oss.sonatype.org
	
	上传方法
	https://www.jianshu.com/p/4105e072a259
	https://blog.csdn.net/oufua/article/details/81506254
	https://blog.csdn.net/anumbrella/article/details/78887195
	
	GitHub 开源代码库的便捷发布渠道
	https://jitpack.io/
	实例介绍
	https://blog.csdn.net/weixin_33724046/article/details/91439563
	
	中央仓库发布实例见
	https://github.com/liyuan3210/project/util2
```