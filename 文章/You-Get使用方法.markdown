# You-Get安装与使用方法

如果没有其他便捷的方法，[You-Get](https://you-get.org/) 是一个微型命令行实用程序，可从Web下载媒体内容（视频，音频，图像）。

这是 `you-get` 从 [YouTube](https://www.youtube.com/watch?v=jNQXAC9IVRw)下载视频:

```console
$ you-get 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
site:                YouTube
title:               Me at the zoo
stream:
    - itag:          43
      container:     webm
      quality:       medium
      size:          0.5 MiB (564215 bytes)
    # download-with: you-get --itag=43 [URL]

Downloading Me at the zoo.webm ...
 100% (  0.5/  0.5MB) ├██████████████████████████████████┤[1/1]    6 MB/s

Saving Me at the zoo.en.srt ... Done.
```

这就是您可能要使用它的原因：

* 您在Internet上享受了一些东西，只是想自己下载就可以了。
* 您可以从计算机上在线观看喜欢的视频，但是禁止保存它们。您感觉自己无法控制自己的计算机。（这不是开放网络应该如何工作的。）
* 您想摆脱任何封闭源技术或专有JavaScript代码，并禁止在计算机上运行Flash之类的东西。
* 您是黑客文化和免费软件的拥护者。

有什么 `you-get` 可以为您服务：

* 从YouTube，Youku，Niconico等流行网站下载视频/音频，等等。（请参阅支持的网站的完整列表）
* 在媒体播放器中流式传输在线视频。没有网络浏览器，没有更多广告。
* 通过抓取网页下载（感兴趣的）图像。
* 下载任意非HTML内容，即二进制文件。

有兴趣吗 现在安装它，并通过示例开始。

您是Python程序员吗？然后检查源并进行分叉！


## 安装

### 先决条件

The following dependencies are necessary:

* **[Python](https://www.python.org/downloads/)**  3.2或以上
* **[FFmpeg](https://www.ffmpeg.org/)** 1.0或以上
* （可选） [RTMPDump](https://rtmpdump.mplayerhq.hu/)

### 选项1：通过pip安装


的正式发行 `you-get` 版在[PyPI](https://pypi.python.org/pypi/you-get)上分发，可以通过[pip](https://en.wikipedia.org/wiki/Pip_\(package_manager\)软件包管理器从PyPI镜像轻松安装。请注意，您必须使用Python 3版本`pip`：

    $ pip3 install you-get

### 选项2：通过[Antigen](https://github.com/zsh-users/antigen)安装（对于Zsh用户）

将以下行添加到您的 `.zshrc`:

    antigen bundle soimort/you-get

### 选项3：从GitHub下载


您可以下载[稳定的](https://github.com/soimort/you-get/archive/master.zip)（与PyPI的最新版本相同）或的[开发](https://github.com/soimort/you-get/archive/develop.zip)（更多修补程序，不稳定的功能）分支`you-get`。解压缩它，然后将包含`you-get`脚本的目录放入您的中`PATH`。



或者，运行

```
$ [sudo] python3 setup.py install
```

要么

```
$ python3 setup.py install --user
```
安装 `you-get` 到永久路径。


### 选项4：Git克隆

即使您不经常使用Python编写代码，这也是所有开发人员的推荐方法。

```
$ git clone git://github.com/soimort/you-get.git
```
然后将克隆的目录放入您的中`PATH`，或运行`./setup.py install` 以安装`you-get`到永久路径。


### 选项5：自制程序（仅限Mac）

您可以 `you-get` 通过以下方式轻松安装：

```
$ brew install you-get
```

### 选项6：pkg（仅限FreeBSD）

您可以 `you-get` 通过以下方式轻松安装：

```
# pkg install you-get
```

### 外壳完成
Bash，Fish和Zsh的完成定义可以在中找到[`contrib/completion`](https://github.com/soimort/you-get/tree/develop/contrib/completion)。请查阅您的Shell手册以了解如何利用它们。


## 升级中

根据您选择安装的选项you-get，可以通过以下方式进行升级：

```
$ pip3 install --upgrade you-get
```

或通过以下方式下载最新版本：

```
$ you-get https://github.com/soimort/you-get/archive/master.zip
```

为了在develop不弄乱PIP的情况下获得最新的分支，可以尝试：

```
$ pip3 install --upgrade git+https://github.com/soimort/you-get@develop
```

## 入门

### 下载影片

收到感兴趣的视频后，您可能需要使用 `--info`/`-i` 选项查看所有可用的质量和格式：


```
$ you-get -i 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
site:                YouTube
title:               Me at the zoo
streams:             # Available quality and codecs
    [ DASH ] ____________________________________
    - itag:          242
      container:     webm
      quality:       320x240
      size:          0.6 MiB (618358 bytes)
    # download-with: you-get --itag=242 [URL]

    - itag:          395
      container:     mp4
      quality:       320x240
      size:          0.5 MiB (550743 bytes)
    # download-with: you-get --itag=395 [URL]

    - itag:          133
      container:     mp4
      quality:       320x240
      size:          0.5 MiB (498558 bytes)
    # download-with: you-get --itag=133 [URL]

    - itag:          278
      container:     webm
      quality:       192x144
      size:          0.4 MiB (392857 bytes)
    # download-with: you-get --itag=278 [URL]

    - itag:          160
      container:     mp4
      quality:       192x144
      size:          0.4 MiB (370882 bytes)
    # download-with: you-get --itag=160 [URL]

    - itag:          394
      container:     mp4
      quality:       192x144
      size:          0.4 MiB (367261 bytes)
    # download-with: you-get --itag=394 [URL]

    [ DEFAULT ] _________________________________
    - itag:          43
      container:     webm
      quality:       medium
      size:          0.5 MiB (568748 bytes)
    # download-with: you-get --itag=43 [URL]

    - itag:          18
      container:     mp4
      quality:       small
    # download-with: you-get --itag=18 [URL]

    - itag:          36
      container:     3gp
      quality:       small
    # download-with: you-get --itag=36 [URL]

    - itag:          17
      container:     3gp
      quality:       small
    # download-with: you-get --itag=17 [URL]
```

默认情况下，顶部的是您将获得的一个。如果这对您来说很酷，请下载：

```
$ you-get 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
site:                YouTube
title:               Me at the zoo
stream:
    - itag:          242
      container:     webm
      quality:       320x240
      size:          0.6 MiB (618358 bytes)
    # download-with: you-get --itag=242 [URL]

Downloading Me at the zoo.webm ...
 100% (  0.6/  0.6MB) ├██████████████████████████████████████████████████████████████████████████████┤[2/2]    2 MB/s
Merging video parts... Merged into Me at the zoo.webm

Saving Me at the zoo.en.srt ... Done.
```

（如果YouTube视频有任何隐藏字幕，它们将与视频文件一起以SubRip字幕格式下载。）

或者，如果您喜欢其他格式（mp4），则只需使用you-get显示的选项即可：

```
$ you-get --itag=18 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
```

**注意：**

* 目前，我们大多数受支持的网站尚未实现格式选择；在这种情况下，默认的下载格式是质量最高的格式。
* `ffmpeg` 是必需的依赖项，用于下载和加入在多个部分（例如，在优酷等某些网站上）流式传输的视频，以及用于1080p或高分辨率的YouTube视频。
* 如果您不想`you-get`在下载后加入视频部分，请使用`--no-merge`/ `-n`选项。


### 下载其他内容

如果您已经有了想要的确切资源的URL，则可以使用以下方法直接下载它：

```
$ you-get https://stallman.org/rms.jpg
Site:       stallman.org
Title:      rms
Type:       JPEG Image (image/jpeg)
Size:       0.06 MiB (66482 Bytes)

Downloading rms.jpg ...
100.0% (  0.1/0.1  MB) ├████████████████████████████████████████┤[1/1]  127 kB/s
```

否则，`you-get` 将抓取网页并尝试找出是否有您感兴趣的东西：

```
$ you-get http://kopasas.tumblr.com/post/69361932517
Site:       Tumblr.com
Title:      kopasas
Type:       Unknown type (None)
Size:       0.51 MiB (536583 Bytes)

Site:       Tumblr.com
Title:      tumblr_mxhg13jx4n1sftq6do1_1280
Type:       Portable Network Graphics (image/png)
Size:       0.51 MiB (536583 Bytes)

Downloading tumblr_mxhg13jx4n1sftq6do1_1280.png ...
100.0% (  0.5/0.5  MB) ├████████████████████████████████████████┤[1/1]   22 MB/s
```

**注意:**

* 此功能是一项实验功能，远非完美。它最适用于从Tumblr和Blogger等受欢迎的网站上抓取大尺寸图像，但是实际上并没有适用于Internet上任何网站的通用模式。

### 在Google视频中搜索并下载

您可以将任何内容传递给you-get。如果该网址无效，you-get将进行Google搜索并为您下载最相关的视频。（这可能不完全是您希望看到的东西，但仍然很有可能。）

```
$ you-get "Richard Stallman eats"
```

### 暂停并继续下载

您可以使用 <kbd>Ctrl</kbd>+<kbd>C</kbd> 中断下载。

临时`.download`文件保留在输出目录中。下次 `you-get` 使用相同的参数运行时，下载进度将从上一个会话恢复。如果文件已完全下载（临时`.download`扩展名已消失），`you-get` 则将跳过下载。

要强制重新下载，请使用 `--force`/`-f` 选项。（**警告：** 这样做会覆盖任何同名的现有文件或临时文件！）


### 设置下载文件的路径和名称

使用 `--output-dir`/`-o` 选项设置路径，并使用 `--output-filename`/`-O` 设置下载文件的名称：


```
$ you-get -o ~/Videos -O zoo.webm 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
```

**提示：**

* 如果您遇到默认视频标题的问题，这些选项会很有用，默认视频标题可能包含特殊字符，这些特殊字符在当前的shell /操作系统/文件系统中无法很好地播放。
* 如果编写脚本来批量下载文件并将其放入具有指定名称的指定文件夹中，这些选项也很有用。

### 代理设定
您可以 `you-get` 通过`--http-proxy`/`-x` 选项指定要使用的HTTP代理：

```
$ you-get -x 127.0.0.1:8087 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
```
但是， `http_proxy`默认情况下将应用系统代理设置（即环境变量）。要禁用任何代理，请使用该 `--no-proxy` 选项。


**提示：**

* 如果你需要使用代理了很多（如果您的网络阻止某些网站），你可能希望使用`you-get`与 [proxychains](https://github.com/rofl0r/proxychains-ng) 和集 `alias you-get="proxychains -q you-get"` （Bash中）。
* 对于某些网站（例如，优酷网），如果您需要访问某些仅在中国大陆可用的视频，则可以选择使用特定的代理从站点中提取视频信息： `--extractor-proxy`/`-y`。


### 观看影片

使用 `--player`/`-p` 选项将视频输入到您选择的媒体播放器中，例如 `mpv` 或 `vlc`，而不是下载它：
```
$ you-get -p vlc 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
```

或者，如果您希望在浏览器中观看视频，而没有广告或评论部分：

```
$ you-get -p chromium 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
```

**提示：**

* 可以使用该 `-p` 选项启动另一个下载管理器，例如 `you-get -p uget-gtk 'https://www.youtube.com/watch?v=jNQXAC9IVRw'`，尽管它们可能不能很好地配合使用。


### 加载cookie
并非所有人都能公开获得所有视频。如果您需要登录帐户才能访问某些内容（例如私人视频），则不可避免地需要 `you-get` 通过`--cookies`/`-c` 选项将浏览器Cookie馈入。


**提示：**
* 到目前为止，我们支持两种浏览器cookie格式：Mozilla  `cookies.sqlite` 和Netscape  `cookies.txt`。

### 重用提取的数据

使用`--url`/`-u` 获取从页面提取的可下载资源URL的列表。使用 `--json` 得到一个抽象提取数据的JSON格式。


**警告：**

* 暂且，这个功能已经 **不** 被稳定和JSON模式可能在未来的重大更改。

## 支持的网站

| Site | URL | Videos? | Images? | Audios? |
| :--: | :-- | :-----: | :-----: | :-----: |
| **YouTube** | <https://www.youtube.com/>    |✓| | |
| **Twitter** | <https://twitter.com/>        |✓|✓| |
| VK          | <http://vk.com/>              |✓|✓| |
| Vine        | <https://vine.co/>            |✓| | |
| Vimeo       | <https://vimeo.com/>          |✓| | |
| Vidto       | <http://vidto.me/>            |✓| | |
| Videomega   | <http://videomega.tv/>        |✓| | |
| Veoh        | <http://www.veoh.com/>        |✓| | |
| **Tumblr**  | <https://www.tumblr.com/>     |✓|✓|✓|
| TED         | <http://www.ted.com/>         |✓| | |
| SoundCloud  | <https://soundcloud.com/>     | | |✓|
| SHOWROOM    | <https://www.showroom-live.com/> |✓| | |
| Pinterest   | <https://www.pinterest.com/>  | |✓| |
| MusicPlayOn | <http://en.musicplayon.com/>  |✓| | |
| MTV81       | <http://www.mtv81.com/>       |✓| | |
| Mixcloud    | <https://www.mixcloud.com/>   | | |✓|
| Metacafe    | <http://www.metacafe.com/>    |✓| | |
| Magisto     | <http://www.magisto.com/>     |✓| | |
| Khan Academy | <https://www.khanacademy.org/> |✓| | |
| Internet Archive | <https://archive.org/>   |✓| | |
| **Instagram** | <https://instagram.com/>    |✓|✓| |
| InfoQ       | <http://www.infoq.com/presentations/> |✓| | |
| Imgur       | <http://imgur.com/>           | |✓| |
| Heavy Music Archive | <http://www.heavy-music.ru/> | | |✓|
| **Google+** | <https://plus.google.com/>    |✓|✓| |
| Freesound   | <http://www.freesound.org/>   | | |✓|
| Flickr      | <https://www.flickr.com/>     |✓|✓| |
| FC2 Video   | <http://video.fc2.com/>       |✓| | |
| Facebook    | <https://www.facebook.com/>   |✓| | |
| eHow        | <http://www.ehow.com/>        |✓| | |
| Dailymotion | <http://www.dailymotion.com/> |✓| | |
| Coub        | <http://coub.com/>            |✓| | |
| CBS         | <http://www.cbs.com/>         |✓| | |
| Bandcamp    | <http://bandcamp.com/>        | | |✓|
| AliveThai   | <http://alive.in.th/>         |✓| | |
| interest.me | <http://ch.interest.me/tvn>   |✓| | |
| **755<br/>ナナゴーゴー** | <http://7gogo.jp/> |✓|✓| |
| **niconico<br/>ニコニコ動画** | <http://www.nicovideo.jp/> |✓| | |
| **163<br/>网易视频<br/>网易云音乐** | <http://v.163.com/><br/><http://music.163.com/> |✓| |✓|
| 56网     | <http://www.56.com/>           |✓| | |
| **AcFun** | <http://www.acfun.cn/>        |✓| | |
| **Baidu<br/>百度贴吧** | <http://tieba.baidu.com/> |✓|✓| |
| 爆米花网 | <http://www.baomihua.com/>     |✓| | |
| **bilibili<br/>哔哩哔哩** | <http://www.bilibili.com/> |✓| | |
| 豆瓣     | <http://www.douban.com/>       |✓| |✓|
| 斗鱼     | <http://www.douyutv.com/>      |✓| | |
| Panda<br/>熊猫 | <http://www.panda.tv/>      |✓| | |
| 凤凰视频 | <http://v.ifeng.com/>          |✓| | |
| 风行网   | <http://www.fun.tv/>           |✓| | |
| iQIYI<br/>爱奇艺 | <http://www.iqiyi.com/> |✓| | |
| 激动网   | <http://www.joy.cn/>           |✓| | |
| 酷6网    | <http://www.ku6.com/>          |✓| | |
| 酷狗音乐 | <http://www.kugou.com/>        | | |✓|
| 酷我音乐 | <http://www.kuwo.cn/>          | | |✓|
| 乐视网   | <http://www.le.com/>           |✓| | |
| 荔枝FM   | <http://www.lizhi.fm/>         | | |✓|
| 秒拍     | <http://www.miaopai.com/>      |✓| | |
| MioMio弹幕网 | <http://www.miomio.tv/>    |✓| | |
| MissEvan<br/>猫耳FM | <http://www.missevan.com/> | | |✓|
| 痞客邦   | <https://www.pixnet.net/>      |✓| | |
| PPTV聚力 | <http://www.pptv.com/>         |✓| | |
| 齐鲁网   | <http://v.iqilu.com/>          |✓| | |
| QQ<br/>腾讯视频 | <http://v.qq.com/>      |✓| | |
| 企鹅直播 | <http://live.qq.com/>          |✓| | |
| Sina<br/>新浪视频<br/>微博秒拍视频 | <http://video.sina.com.cn/><br/><http://video.weibo.com/> |✓| | |
| Sohu<br/>搜狐视频 | <http://tv.sohu.com/> |✓| | |
| **Tudou<br/>土豆** | <http://www.tudou.com/> |✓| | |
| 虾米     | <http://www.xiami.com/>        |✓| |✓|
| 阳光卫视 | <http://www.isuntv.com/>       |✓| | |
| **音悦Tai** | <http://www.yinyuetai.com/> |✓| | |
| **Youku<br/>优酷** | <http://www.youku.com/> |✓| | |
| 战旗TV   | <http://www.zhanqi.tv/lives>   |✓| | |
| 央视网   | <http://www.cntv.cn/>          |✓| | |
| Naver<br/>네이버 | <http://tvcast.naver.com/>     |✓| | |
| 芒果TV   | <http://www.mgtv.com/>         |✓| | |
| 火猫TV   | <http://www.huomao.com/>       |✓| | |
| 阳光宽频网 | <http://www.365yg.com/>      |✓| | |
| 西瓜视频 | <https://www.ixigua.com/>      |✓| | |
| 快手 | <https://www.kuaishou.com/>      |✓|✓| |
| 抖音 | <https://www.douyin.com/>      |✓| | |
| TikTok | <https://www.tiktok.com/>      |✓| | |
| 中国体育(TV) | <http://v.zhibo.tv/> </br><http://video.zhibo.tv/>    |✓| | |
| 知乎 | <https://www.zhihu.com/>      |✓| | |

对于不在列表中的所有其他站点，通用提取器将负责从页面中查找和下载有趣的资源。

### 已知错误

如果某件东西坏了，`you-get`不能让您得到想要的东西，请不要惊慌。（是的，这种情况一直发生！）

在<https://github.com/soimort/you-get/wiki/Known-Bugs>上检查是否已经是已知问题。如果不是，请遵循有关[如何报告问题](https://github.com/soimort/you-get/blob/develop/CONTRIBUTING.md)的准则。



## 参与进来

您可以在Gitter频道 [#soimort/you-get](https://gitter.im/soimort/you-get) 上与我们联系（这是为Gitter [设置IRC客户端](http://irc.gitter.im)的方式）。如果您有关于的快速问题`you-get`，请在那里提问。

如果您要报告问题或做出贡献，请确保首先阅读 [准则](https://github.com/soimort/you-get/blob/develop/CONTRIBUTING.md)。




[原文地址：](https://github.com/soimort/you-get)
