# FFmpeg使用H264编码转换视

H264（即MPEG4 AVC）是目前比较流行的视频编码格式，相对MPEG2编码而言，在画质大致相同的情况下能将视频大小再压缩到50%~25%，即如果一个MPEG2（如DVD）视频大小是1GB，用H264编码能缩小到250MB左右。另外H264编码的视频还能直接在浏览器（如Chrome）和移动设备（如iPhone、Android手机）上直接播放。
如果你有一堆家庭视频（旧款的家用DV一般是MPEG2格式）想刻录到光盘保存，或者有一堆手机不直接支持的格式的视频想在手机上播放，那么用H264编码转换和压缩它们是一个不错的选择。

mencoder 是一个很方便的视频编码程序，它几乎支持所有的视频格式，而且参数丰富、速度快。

首选你需要安装 mencoder 程序（下面分别是 Archlinux, Fedora, Ubuntu下的安装方式）：

```
$ sudo pacman -S memcoder
$ sudo yum install mencoder
$ sudo apt-get install mencoder
```
然后就可以查看你当前系统支持哪些视频和音频编码器，以及支持哪些封装格式了：
```
$ mencoder -ovc help
$ mencoder -oac help
$ mencoder -of help
```

如果看到有x264视频编码器、有mp3lame音频编码器、以及有mp4封装格式，那么就可以开始下面的编码转换了，否则你可能需要安装相应的音频和视频编码器，一般安装 ffmpeg 组件就会同时附带这些编码器。

压缩一段MPEG2视频：

```
$ mencoder m001.mpg -o m001.mp4 -oac mp3lame -ovc x264 -of lavf -vf lavcdeint
```
上面命令中的 m001.mpg 和 m001.mp4 分别是输入和输出文件名，-oac 用于指定音频编码器，-ovc 指定视频编码器， -of 指定输出文件封装方式，lavf表示输出文件封装方式由输出的文件名（的扩展名）决定（比如m001.mp4表示用mp4封装，m001.avi表示用avi封装），最后 -vf lavcdeint 参数用于去除视频中的拉丝条纹（锯齿纹），如果没有的话不要这个参数也可以。

h264的编码过程比较耗时，比如 AMD 四核2.8G的编码速度大概是 30fps，大概是视频正常播放所需的时间。

如果待编码转换的视频文件很多，则最好写一个批量处理的脚本：

```
#!/bin/bash
find . -type f ( -name "*.mpg" -o -name "*.mpeg" )|while read line;do
echo $line
mencoder $line -o ${line}.mp4 -oac mp3lame -ovc x264 -of lavf -vf lavcdeint
done
```

执行上面的脚本会将当前目录里所有后缀名为“mpg”和“mpeg”的视频编码为H264格式。


[参考](https://www.strongd.net/?m=201609)
