# 操作系统

### [1.gcc](gcc/index.md)

### [2.assembly](assembly/index.md)

### [3.linux-kernel](linux-kernel/index.md)

### 待解决

```
1.select poll epoll原理
https://www.bilibili.com/video/BV1qJ411w7du
https://www.cnblogs.com/yuanwebpage/p/12916424.html

2.linux内核源码克隆问题
C:\Users\liyuan\Desktop\git\linux-kernel>git clone git@mygitee:liyuan3210/linux-6.2.8.git linux-6.2.8_COPY
Cloning into 'linux-6.2.8_COPY'...
remote: Enumerating objects: 84334, done.
remote: Counting objects: 100% (84334/84334), done.
remote: Compressing objects: 100% (76389/76389), done.
remote: Total 84334 (delta 7070), reused 84334 (delta 7070), pack-reused 0
Receiving objects: 100% (84334/84334), 234.63 MiB | 6.03 MiB/s, done.
Resolving deltas: 100% (7070/7070), done.
error: invalid path 'drivers/gpu/drm/nouveau/nvkm/subdev/i2c/aux.c'
fatal: unable to checkout working tree
warning: Clone succeeded, but checkout failed.
You can inspect what was checked out with 'git status'
and retry with 'git restore --source=HEAD :/'

能看见：
$ git status
$ git restore --source=HEAD :/

描述：
$ git status
C:\Users\liyuan\Desktop\git\linux-kernel\linux-6.2.8_COPY>git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    COPYING
        deleted:    CREDITS
        deleted:    Documentation/.gitignore
        deleted:    Documentation/ABI/README
		............

解决办法：
https://blog.csdn.net/weixin_36212725/article/details/119875787
https://blog.csdn.net/dake1994/article/details/90601092

$ git config --system core.longpaths true
$ git add .
$ git commit
关闭运行的git的cmd窗口重新打开

3.INTEL芯片文档下载(IA32模式)
官网：https://www.intel.com
根据如下视频，“c05.01创建GDT表及其表项”章节
https://www.bilibili.com/video/BV1jV4y1H7Gj
文档：Intel64 and IA-32 Architectures Software Developer`s Manual
32位保护模式查阅,只关注卷3(Volume3)文档,2,3,4,5,6,7,8,9即可
```

