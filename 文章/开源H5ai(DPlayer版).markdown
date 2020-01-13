# 开源h5ai-DPlayer版，更新支持切片视频播放

## 更新如下：

1. h5ai更新到0.29.2

2. DPlayer更新到1.25.0

3. 增加hls切片播放支持

4. 支持登录验证，默认用户名: `admin` 密码: `20190608`

5. 支持vtt格式同视频文件名字幕文件，可用ffmpeg将普通字幕文件转换为vtt字幕文件。

6. 修复切片文件夹路径

## 下载：

https://github.com/Pearlulu/h5ai_dplayer_hls/releases

1. 修改切片目录称为带文件类型后缀，避免同名mp4文件和mkv文件等目录冲突。
```
old:
├──  __abcd__
 │   └──  video.m3u8
└──  abcd.mp4
 new:
├──  __abcd.mp4__
 │   └──  video.m3u8
└──  abcd.mp4
```
2. 恢复连续播放多个视频文件内存消耗增加的问题
# 开源h5ai_DPlayer版，更新支持切片视频播放

## 更新如下：

1. h5ai更新到0.29.2

2. DPlayer更新到1.25.0

3. 增加hls切片播放支持

4. 支持登录验证，默认用户名: `admin` 密码: `20190608`

5. 支持vtt格式同视频文件名字幕文件，可用ffmpeg将普通字幕文件转换为vtt字幕文件。

6. 修复切片文件夹路径

## 下载：

https://github.com/Pearlulu/h5ai_dplayer_hls/releases

1. 修改切片目录称为带文件类型后缀，避免同名mp4文件和mkv文件等目录冲突。
```
old:
├──  __abcd__
 │   └──  video.m3u8
└──  abcd.mp4
 new:
├──  __abcd.mp4__
 │   └──  video.m3u8
└──  abcd.mp4
```
2. 恢复连续播放多个视频文件内存消耗增加的问题

**文件**

[**h5ai_dplayer_hls_20190610.zip**](https://github.com/Pearlulu/h5ai_dplayer_hls/releases/download/0610/h5ai_dplayer_hls_20190610.zip)


## 关于切片视频播放：

1. 例如视频在 `/video/abcd.mp4` ，那么会自动寻找 `/video/__abcd.mp4__/video.m3u8`。

也就是会寻找" `__视频文件名__` "文件夹下的切片文件。如果没有切片文件会播放原始视频。
切片文件夹和文件都是 **隐藏** 的，**不会在h5ai中显示**。

2. 切片文件可自行用 **ffmpeg** 生成，例如

```
mkdir __abcd01.mp4__
ffmpeg -i "abcd01.mp4" -c copy -bsf:v h264_mp4toannexb -hls_time 6 -hls_list_size 0 -hls_segment_filename "__abcd01.mp4__/%04d.ts" "__abcd01.mp4__/video.m3u8"
```

切片有很多种，复制视频流、转码、加水印、加密等等，只要video.m3u8文件生成正确就都可以播放。有兴趣的可以自己写脚本，配合aria2下载完视频后自动切片，还有一些切片视频的特殊用法就不多说了。

3. 在线看几个G那种比较大的视频文件跳转缓冲很吃力，切片后就舒服多了，可以任意拖进度条跳转。

## 关于登录

自用的弄得很简易，够用就行。

如果不想要用户名密码，把 `_h5ai/public/index.php` 里第二行 `include 'login.php'` 。

修改用户名密码在 `_h5ai/public/login.php` 。

![](https://www.locmjj.com/wp-content/uploads/image/20190612/1560338041849554.png)


## 参考：
https://www.locmjj.com/201.html
https://github.com/Pearlulu/h5ai_dplayer_hls/releases

**文件**

[**h5ai_dplayer_hls_20190610.zip**](https://github.com/Pearlulu/h5ai_dplayer_hls/releases/download/0610/h5ai_dplayer_hls_20190610.zip)


## 关于切片视频播放：

1. 例如视频在 `/video/abcd.mp4` ，那么会自动寻找 `/video/__abcd.mp4__/video.m3u8`。

也就是会寻找" `__视频文件名__` "文件夹下的切片文件。如果没有切片文件会播放原始视频。
切片文件夹和文件都是 **隐藏** 的，**不会在h5ai中显示**。

2. 切片文件可自行用 **ffmpeg** 生成，例如

```
mkdir __abcd01.mp4__
ffmpeg -i "abcd01.mp4" -c copy -bsf:v h264_mp4toannexb -hls_time 6 -hls_list_size 0 -hls_segment_filename "__abcd01.mp4__/%04d.ts" "__abcd01.mp4__/video.m3u8"
```

切片有很多种，复制视频流、转码、加水印、加密等等，只要video.m3u8文件生成正确就都可以播放。有兴趣的可以自己写脚本，配合aria2下载完视频后自动切片，还有一些切片视频的特殊用法就不多说了。

3. 在线看几个G那种比较大的视频文件跳转缓冲很吃力，切片后就舒服多了，可以任意拖进度条跳转。

## 关于登录

自用的弄得很简易，够用就行。

如果不想要用户名密码，把 `_h5ai/public/index.php` 里第二行 `include 'login.php'` 。

修改用户名密码在 `_h5ai/public/login.php` 。



## 参考：
https://www.locmjj.com/201.html
https://github.com/Pearlulu/h5ai_dplayer_hls/releases
