## 音频与视频处理

#### 声道介绍（2.1，4.0，4.1，5.1，7.1）
```
https://zhidao.baidu.com/question/942726010171563932.html
```

#### 开源图像处理库
```
https://blog.csdn.net/xiaotoly/article/details/51042308
```

#### ffmpeg和opencv
```
https://blog.csdn.net/zhiyuan2021/article/details/118708538
```

#### OpenGL详细解读
```
https://zhuanlan.zhihu.com/p/56693625
```

#### 图片编辑软件
```
18个免费替代Photoshop的图像编辑软件
    https://blog.csdn.net/weixin_44305576/article/details/86522925

适合windows,linux,mac:
    Gimp图片编辑软件
    使用教程：https://www.bilibili.com/video/BV1bt411F7Ug
    Flatpak的构建安装（类似ubuntu系统的snap）
    https://zhuanlan.zhihu.com/p/55299546

适合window：
    图片编辑器（PhotoDemon）适合windows
    https://photodemon.org/
```

#### 录屏工具
```
适合windows:
    https://www.captura.org/
    https://mathewsachin.github.io/Captura
    Captura与ffmpeg下载
    https://github.com/MathewSachin/Captura/releases/download/v8.0.0/Captura-Portable.zip
    https://johnvansickle.com/ffmpeg/releases/ffmpeg-release-amd64-static.tar.xz
    录屏软件 Captura 及 FFmpeg 安装配置教程
    https://zhuanlan.zhihu.com/p/294895620?utm_source=qq

录屏直播(windows，mac,linux)
    obs官网：
    https://obsproject.com/
```

#### ffmpeg工具使用
```
视频转码：
ffmpeg -i 喜欢你.mov 喜欢你.mp4

截取图片：
ffmpeg -i 喜欢你.mp4 -ss 00:19 -f image2 -vframes 1 xhn.jpg

移除水印：
https://blog.csdn.net/qq_34777982/article/details/124038072
* 先使用ffmpeg截图，然后使用“Gimp图片编辑软件”定位位置，长宽参数
ffmpeg -i 喜欢你.mp4 -vf "delogo=x=43:y=29:w=49:h=41:show=0" -c:a copy 喜欢你-以去除.mp4

视频裁剪（视频有黑边）：
ffmpeg -i 喜欢你.mp4 -ss 00:03:47 -to 00:04:30 -c copy 喜欢你-已裁剪.mp4

视频合并：
ffmpeg -f concat -safe 0 -i vlist.txt 灰色轨迹-大地-喜欢你-all.mp4
“vlist.txt”文件内容：
file 'wei/灰色轨迹-wei.mp4'
file 'wei/大地-wei.mp4'
file 'wei/喜欢你-wei.mp4'
```