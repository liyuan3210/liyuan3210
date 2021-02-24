# jwt与spring_security

## 一.jwt

实例：https://github.com/liyuan3210/java/tree/master/jwt_spring-security/jwt-spring-security

开启/关闭(jwt)：JwtSpringSecurityApplication.java启动类注释掉如下方法

```
//    @Bean
//    public FilterRegistrationBean jwtFilter() {
//        final FilterRegistrationBean registrationBean = new FilterRegistrationBean();
//        JwtAuthenticationFilter filter = new JwtAuthenticationFilter();
//        registrationBean.setFilter(filter);
//        return registrationBean;
//    }
```

## 二.spring_security

**概述：**

spring_security包含两部分，**用户认证**，**用户授权**，是重量级框架

不局限web环境，可以脱离web使用

**同款产品对比：**

shiro：是apache旗下轻量级控制框架

### 1.入门案例

1.1>创建spring boot工程，引入pom包(与spring boot)无缝集成

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-security</artifactId>
	<version>2.2.1.RELEASE</version>
</dependency>
```

 1.2>创建测试类

com.liyuan3210.jwtspringsecurity.springsecurity.controller.TestController3.java

http://127.0.0.1:8080/test/hello

1.3>浏览器访问

http://127.0.0.1:8080/test/hello

Using generated security password: b197d0c2-a1c7-4040-82b2-75d09cfeffcc（启动时控制台打印）

默认用户名/密码：user / b197d0c2-a1c7-4040-82b2-75d09cfeffcc



### 2.基本原理

spring_security本质是一个过滤器链（有很多过滤器)

new FilterSecurityInterceptor();		//方法级过滤器(过滤哪些方法可以放行)
new ExceptionTranslationFilter();		//异常过滤器
new UsernamePasswordAuthenticationFilter();	//用户认证过滤器

2.1过滤器如何进行加载？

DelegatingFilterProxy(如果不使用spring boot,首先要配置这个过滤器,spring boot已帮我们做了)

用户/密码登录（自定义认证，两个重要接口）：

UserDetailsService

PasswordEncoder



### 3.权限方案

用户名/密码认证有三种方案：

3.1>.第一种：通过配置文件

```
spring.security.user.name=admin
spring.security.user.password=123456
```

验证：http://127.0.0.1:8080/test/hello

3.2>.第二种：通过配置类

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
        String password = passwordEncoder.encode("123");
        auth.inMemoryAuthentication().withUser("lucy").password(password).roles("admin");
    }

    @Bean
    PasswordEncoder password() {
        return new BCryptPasswordEncoder();
    }
}
```

验证：http://127.0.0.1:8080/test/hello

3.3>.第三种：自定义编写实现类

编写SecurityConfig配置类：

```java
@Configuration
public class SecurityConfigTest extends WebSecurityConfigurerAdapter {
	@Autowired
	private UserDetailsService userDetailsService;
	
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		auth.userDetailsService(userDetailsService).passwordEncoder(password());
	}
	
	@Bean
	PasswordEncoder password() {
		return new BCryptPasswordEncoder();
	}
}
```

编写MyUserDetailsService实现类（UserDetailsService）

```java
@Service("userDetailsService")
public class MyUserDetailsService implements UserDetailsService {

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
		return new User("mary",new BCryptPasswordEncoder().encode("123"),auths);
	}

}
```

验证：http://127.0.0.1:8080/test/hello

mary	/	123



### 4.查询数据库进行认证

1.整合MyBatisPlus完成操作，使用引入相关依赖

```
 <!-- db -->
 <dependency>
     <groupId>com.baomidou</groupId>
     <artifactId>mybatis-plus-boot-starter</artifactId>
     <version>3.0.5</version>
 </dependency>
 <!--mysql-->
 <dependency>
     <groupId>mysql</groupId>
     <artifactId>mysql-connector-java</artifactId>
 </dependency>
```

配置数据库：

```
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://192.168.1.136:3306/test?serverTimezone=GMT%2B8
spring.datasource.username=adm
spring.datasource.password=adm!>KTH*23L6
```

2.创建数据库表

```
CREATE TABLE `test`.`users` (
  `id` INT NOT NULL,
  `username` VARCHAR(45) NULL,
  `password` VARCHAR(45) NULL,
  PRIMARY KEY (`id`));
--插入数据  
INSERT INTO `test`.`users` (`id`, `username`, `password`) VALUES ('1', 'lucy', '123');
INSERT INTO `test`.`users` (`id`, `username`, `password`) VALUES ('2', 'mary', '456');
```

3.编写entity,mapper类

4.实现UserDetailsService接口，MyUserDetailsService类编写

5.添加注解MapperScan

@MapperScan("com.liyuan3210.jwtspringsecurity.springsecurity.mapper")

### 5.微服务方案







