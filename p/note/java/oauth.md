# 微服务登录授权方案

一．session与cookies关系(token)

浏览器同源策略（浏览器自动判断，session与cookies作用域）

跨域(不同源)

​			iframe,img,<script src="url">（只能get读取）

跨域实现(框架)

​			jsonp	只能get(iframe,img,src)通过回调函数，老版本都支持

​			cors	浏览器+服务器（产生共识），新版本支持，现在用的用的一般都支持

二．CSRF与XSS

XSS	跨站点脚本攻击，流量攻击（黑掉足够多的站点,通过<script src=url />嵌入攻击的url），或者可以通过微博，富文本编辑嵌入script 

解决办法，使用post

CSRF	伪装请求(可以跨过需要登录的站点，利用本地已登录账号进行调用里面的api)

三．spring security(shiro与spring security)

shiro与spring security

shiro轻量级框架，spring security重量级框架

3.1spring security集成入门

​		1>.spring boot项目添加依赖

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
​		2>.编写controller

​		启动默认用户user,默认密码在控制台(Using generated security password: aa1672de-e829-4dfe-992d-84228ffc804a)

3.2spring security原理

​		1>.spring security本质是一个过滤器链

​		2>.过滤器如何加载

​			spring boot对spring security实现自动化加载、

​			默认过滤器DelegatingFilterProxy

​		3>.两个重要接口

​			UserDetailsService(数据库查用户名与密码)

​			PasswordEncoder(对密码进行加密)

过滤器UsernamePasswordAuthenticationFilter

（继承此类，重写attemptAuthentication）,

成功会调用successfulAuthentication，失败会调用unsuccessfulAuthentication

​		

四．jwt使用

五．解决同源思想sso(cas方案)

​		1.session同步

​		2.redis...

​		3.oauth(统一网关)

​		4.CAS