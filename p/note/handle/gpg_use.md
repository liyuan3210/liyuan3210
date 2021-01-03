# gpg加解密

```
下载安装
	https://www.gnupg.org/

一.创建,删除密钥对
	1.创建
	gpg --gen-key 或 	//默认只有2两年有效时间
	gpg --full-gen-key	//生成密钥对,可以设置加密方式,永久生效及过期时间
	创建gpg密钥这里省略...
	2.删除
	gpg --delete-secret-key 用户id[或 E08B5F03F3EA2F0C]		//先删除私钥
	gpg --delete-key 用户id[或 E08B5F03F3EA2F0C]			//然后才可以删除公钥

二.查看与发布(maven发布时需要send-keys)
	1.查看
	gpg --list-keys				//查看公钥
	gpg --list-secret-keys		//查看私钥

	2.发布(发布一台机器,通过交换机制,所有的公钥服务器最终都会包含你的公钥)
	如果要立马生效可以依次发布
	gpg --keyserver hkp://pool.sks-keyservers.net --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
	gpg --keyserver hkp://pool.sks-keyservers.net:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
	gpg --keyserver hkp://keyserver.ubuntu.com:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
	gpg --keyserver hkp://keys.gnupg.net:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
	gpg --keyserver hkp://keys.gnupg.net:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
	查看是否生效--recv-keys
	gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
	
	3.撤销证书(以后密钥作废时，可以请求外部的公钥服务器撤销你的公钥。？)
	gpg --gen-revoke [用户ID]
	gpg --gen-revoke E08B5F03F3EA2F0C
	生成撤销证书
	gpg --output d:/revc-mvnRepo.cert --gen-revoke liyuan3210
	gpg -a -o d:/revc-mvnRepo-cert.txt --gen-revoke E08B5F03F3EA2F0C
	撤销使用
	gpg --gen-revoke E08B5F03F3EA2F0C
	
	4.查找密钥服务器
	gpg --search-keys li.yuan3210@163.com
	找到hash id
	
	5.公钥指纹(?)
	由于公钥服务器没有检查机制，任何人都可以用你的名义上传公钥，所以没有办法保证服务器上的公钥的可靠性。
	通常，你可以在网站上公布一个公钥指纹，让其他人核对下载到的公钥是否为真。
	fingerprint参数生成公钥指纹
	
	gpg --fingerprint [用户ID]
	
	指纹吊销
	https://www.oschina.net/translate/pgp-tutorial?lang=chs&p=2
	
三.导入导出
	1.导出
	gpg --output d:/mygpgkey_pub.gpg --armor --export liyuan3210 [用户id 或 E08B5F03F3EA2F0C]					//导出公钥
	gpg --output d:/mygpgkey_sec.gpg --armor --export-secret-key liyuan3210 [用户id 或 E08B5F03F3EA2F0C]		//导出私钥需要输入密码

	2.导入
	gpg --import d:/mygpgkey_pub.gpg									//导入公钥
	gpg --allow-secret-key-import --import d:/mygpgkey_sec.gpg			//导入私钥

	mvnRepo.gpg文件导入方法:
	https://www.gnupg.org	//下载安装
	导入文件
	
四.加密解密(不需要send-keys,需要导入导出用来解密)
	加密
	gpg --recipient liyuan3210 --output demo.jiami.txt --encrypt demo.txt
	
	解密(需要输入密码)
	gpg --output demo2.txt --decrypt demo.jiami.txt	
	或者
	gpg demo.jiami.txt (根据提示输入输出文件名称)
	...

五.签名
	gpg --sign demo.txt								//生成demo.txt.gpg文件
	gpg --clearsign demo.txt						//生成ASCII码的签名文件,当前目录下生成demo.txt.asc文件
	
	gpg --detach-sign demo.txt						//生成单独的签名文件,生成demo.txt.sig文件
	gpg --armor --detach-sign demo.txt				//采用ASCII码形式
	
	签名+加密(需要导出公钥,私钥)
	gpg --local-user liyuan3210 --recipient testjm --armor --sign --encrypt demo.txt			//加密需要本地公钥liyuan3210与testjm,解密只需要testjm
	
	gpg --local-user liyuan3210 --recipient liyuan3210 --armor --sign --encrypt demo.txt		//生成文件demo.txt.asc,liyuan3210自己加密,自己解密
	
	签名+加密(解密)：
	gpg demo.txt.asc	//提示输入解密后的文件名称
	
	...
	
	验证签名
	gpg --verify demo.txt.asc demo.txt
	
	gpg: BAD signature from			//验证签名失败
	gpg: Good signature from		//验证签名成功
	
六.实例
	了解吊销流程,验证
	创建一个无期限限制的
	
扩展:
	1>.PGP,OpenPGP,GPG(GnuPG)关系
	引用:
	https://www.jianshu.com/p/d2ac9f0981d0
	
	PGP(很早就有的加密方式,被Symantec收购,现在收费)
	↓
	OpenPGP(依据PGP做出的加密标准)
	↓
	GPG(Gnu根据OpenPGP标准(RFC4880)编写的加密软件GnuPG,
	GPG与PGP互通,双方都相互识别对方公私钥)
----------------------------------------------------------------------------------------------------
gpg问题及解决：

gpg密钥生成(有期限的)：
gpg --gen-key
Real name: liyuan3210
Email address: li.yuan3210@163.com
You selected this USER-ID:
    "liyuan3210 <li.yuan3210@163.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key E08B5F03F3EA2F0C marked as ultimately trusted
gpg: directory 'C:/Users/Administrator/AppData/Roaming/gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as 'C:/Users/Administrator/AppData/Roaming/gnupg/openpgp-revocs.d\2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C.rev'
public and secret key created and signed.

pub   rsa2048 2019-11-08 [SC] [expires: 2021-11-07]
      2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
uid                      liyuan3210 <li.yuan3210@163.com>
sub   rsa2048 2019-11-08 [E] [expires: 2021-11-07]


maven发布命令
mvn clean deploy -P release -Dgpg.passphrase=liyuan123abc
-------------------------------------------------------------------------------------------------------
问题1
[INFO] --- maven-gpg-plugin:1.6:sign (sign-artifacts) @ liyuan3210-util2 ---
gpg: can't connect to the agent: Invalid value passed to IPC
gpg: can't connect to the agent: Invalid value passed to IPC
gpg: keydb_search failed: No agent running
gpg: no default secret key: No agent running
gpg: signing failed: No agent running
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  15.646 s
[INFO] Finished at: 2019-11-08T14:55:05+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-gpg-plugin:1.6:sign (sign-artifacts) on project liyuan3210-util2: Exit code: 2 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException

解决办法
where gpg	//查看gpg命令目录
配置setting.xml 指定gpg命令实际使用目录
注意：
如果装了git不能使用git下gpg命令目录
###################################################################################
问题2
[INFO] Building jar: D:\git\project\util2\target\liyuan3210-util2-1.1.3-javadoc.jar
[INFO]
[INFO] --- maven-gpg-plugin:1.6:sign (sign-artifacts) @ liyuan3210-util2 ---
gpg: invalid size of lockfile 'C:\Users\Administrator\AppData\Roaming\gnupg/gnupg_spawn_agent_sentinel.lock'
gpg: cannot read lockfile
gpg: can't connect to the agent: Invalid argument
gpg: invalid size of lockfile 'C:\Users\Administrator\AppData\Roaming\gnupg/gnupg_spawn_agent_sentinel.lock'
gpg: cannot read lockfile
gpg: can't connect to the agent: Invalid argument
gpg: keydb_search failed: No agent running
gpg: no default secret key: No agent running
gpg: signing failed: No agent running
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  3.547 s
[INFO] Finished at: 2019-11-08T16:51:29+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-gpg-plugin:1.6:sign (sign-artifacts) on project liyuan3210-util2: Exit code: 2 -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException

解决办法
where gpg	//查看gpg命令目录
配置setting.xml 指定gpg命令实际使用目录
注意：
如果装了git不能使用git下gpg命令目录
###################################################################################
windows命令行安装


@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

Waiting for operation to complete...
.....

[ERROR] Rule failure while trying to close staging repository with ID "comliyuan3210-1056".
[ERROR]
[ERROR] Nexus Staging Rules Failure Report
[ERROR] ==================================
[ERROR]
[ERROR] Repository "comliyuan3210-1056" failures
[ERROR]   Rule "signature-staging" failures
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://pool.sks-keyservers.net:11371/&gt;http://pool.sks-keyservers.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keyserver.ubuntu.com:11371/&gt;http://keyserver.ubuntu.com:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keys.gnupg.net:11371/&gt;http://keys.gnupg.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://pool.sks-keyservers.net:11371/&gt;http://pool.sks-keyservers.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keyserver.ubuntu.com:11371/&gt;http://keyserver.ubuntu.com:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keys.gnupg.net:11371/&gt;http://keys.gnupg.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://pool.sks-keyservers.net:11371/&gt;http://pool.sks-keyservers.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keyserver.ubuntu.com:11371/&gt;http://keyserver.ubuntu.com:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keys.gnupg.net:11371/&gt;http://keys.gnupg.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://pool.sks-keyservers.net:11371/&gt;http://pool.sks-keyservers.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keyserver.ubuntu.com:11371/&gt;http://keyserver.ubuntu.com:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]     * No public key: Key with id: (e08b5f03f3ea2f0c) was not able to be located on &lt;a href=http://keys.gnupg.net:11371/&gt;http://keys.gnupg.net:11371/&lt;/a&gt;. Upload your public key and try the operation again.
[ERROR]
[ERROR]
[ERROR] Cleaning up local stage directory after a Rule failure during close of staging repositories: [comliyuan3210-1056]
[ERROR]  * Deleting context 102bf46ce99e6.properties
[ERROR] Cleaning up remote stage repositories after a Rule failure during close of staging repositories: [comliyuan3210-1056]
[ERROR]  * Dropping failed staging repository with ID "comliyuan3210-1056" (Rule failure during close of staging repositories: [comliyuan3210-1056]).

Waiting for operation to complete...
..

[ERROR] Remote staging finished with a failure: Staging rules failure!
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  04:24 min
[INFO] Finished at: 2019-11-08T17:15:48+08:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.sonatype.plugins:nexus-staging-maven-plugin:1.6.8:deploy (injected-nexus-deploy) on project liyuan3210-util2: Remote staging failed: Staging rules failure! -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException

解决方法(发布提示地址公钥服务器)
gpg --keyserver hkp://pool.sks-keyservers.net --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
gpg --keyserver hkp://pool.sks-keyservers.net:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
gpg --keyserver hkp://keyserver.ubuntu.com:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
gpg --keyserver hkp://keys.gnupg.net:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
gpg --keyserver hkp://keys.gnupg.net:11371 --send-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C
查看
gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 2E5EB6F749B04FCF81523B19E08B5F03F3EA2F0C

<profile>
<id>ossrh</id>
<activation>
	<activeByDefault>true</activeByDefault>
</activation>
<properties>
	<gpg.executable>gpg</gpg.executable>
	<gpg.passphrase>liyuan123abc</gpg.passphrase>
	<gpg.executable>D:\soft\dev\GnuPG\bin\gpg.exe</gpg.executable>
	<gpg.homedir>C:\Users\Administrator\AppData\Roaming\gnupg</gpg.homedir>
</properties>
</profile>

发布成功
Uploaded to ossrh: https://oss.sonatype.org:443/service/local/staging/deployByRepositoryId/comliyuan3210-1057/com/liyuan3210/liyuan3210-util2/1.1.3/liyuan3210-util2-1.1.3-sources.jar.asc (499 B at 549 B/s)
[INFO]  * Upload of locally staged artifacts finished.
[INFO]  * Closing staging repository with ID "comliyuan3210-1057".

Waiting for operation to complete...
.......

[INFO] Remote staged 1 repositories, finished with success.
[INFO] Remote staging repositories are being released...

Waiting for operation to complete...
......

[INFO] Remote staging repositories released.
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:41 min
[INFO] Finished at: 2019-11-08T17:22:04+08:00
[INFO] ------------------------------------------------------------------------

```

