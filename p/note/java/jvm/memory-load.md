# 3.内存加载过程

示例源码：

```
https://github.com/liyuan3210/java/jvm
com.liyuan3210.jvm.classloader
```

## 3.1类加载初始化

![](img/jvm-3.1.png)

```
loading
	类加载
linking
	verification:校验class文件格式
	preparation:赋默认值(如代码声明变量i=2,这一步赋0)
	resolution:class文件常量池符号引用转换成直接内存地址
initializing
	调用静态代码块赋初始值
```

＇T01_ClassLoadingProcedure＇

## 3.2类加载器

![](img/jvm-3.2.png)



双亲委派 是为了安全(防止自己定义覆盖java核心类型)

## 3.3类加载器范围

来自Launcher源码

```
- sun.boot.class.path
	.Bootstrap.ClassLoader加载路径
- java.ext.dir
	.ExtensionClassLoader加载路径
- java.class.path
	.AppClassLoader加载路径
```

com.liyuan3210.jvm.classloader.T03_ClassLoaderScope

## 3.4自定义加载器

T05_LoadClassByHand

T06_MSBClassLoader	自定义加载

T11_ClassReloading1，T12_ClassReloading2　自定义重新load(实现热部署)

## other:

CompilerApi：	在内存中编译源码成class

.class文件加密：T07_MSBClassLoaderWithEncription

.lazyloading用到时候才去加载：T08_LazyLoading

```
．严格讲应该叫lazyloading
．jvm规范并没有规定何时加载
．但是严格规定了什么时候必须初始化
	－new getstatic putstatic invokestatic指令，访问final变量除外
	－java.lang.reflect对类进行反射调用时
	－初始化子类的时候，父类首先初始化
	－虚拟机启动时，被执行的主类必须初始化
	－动态语言支持java.lang.invoke.MethhodHandle解析的结果为
	REF_getstatic REF_putstatic REF_invokestatic的方法句柄时，该类必须初始化
```

.混合模式：T09_WayToRun（需要指定参数）

```
.解释器
	－bytecode intepreter
.JIT
	－Just In-Time compiler
.混合模式
	－混合使用解释器＋热点代码编译
	－起始阶段采用解释执行
	－热点代码检测
		．多次被调用的方法（方法计数器：检测方法执行频率）
		．多次被调用的循环（循环计数器：检测循环执行频率）
		．进行编译
参数：
.-Xmixed 默认为混合模式开始解释执行，启动速度较快对热点代码实行检测和编译
.-Xint 使用编译模式，启动很快执行稍慢
.-Xcomp 使用纯编译模式，执行很快，启动很慢

demo:
com.liyuan3210.jvm.classloader.T009_WayToRun
```

​		