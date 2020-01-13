# H5ai


## 现代HTTP Web服务器索引

[**立即下载**](https://release.larsjung.de/h5ai/h5ai-0.29.2.zip) | [**官网下载最新**](https://release.larsjung.de/h5ai/h5ai-0.29.2.zip) | [**演示一**](https://larsjung.de/h5ai/demo/) | [**演示二**](https://release.larsjung.de/)



h5ai是HTTP Web服务器的现代文件索引器，重点放在您的文件上。目录以吸引人的方式显示，并且通过不同的视图，面包屑和树概述来增强对目录的浏览。最初，h5ai是HTML5 Apache Index的首字母缩写，但现在它也支持其他Web服务器。

查看启用了大多数功能的演示目录。简化的示例和我的实际用例是此页面上项目的发布目录。

需要PHP 5.5以上版本，并与Apache httpd，lighttpd和nginx兼容。使用最新版本的基于Chromium的浏览器，Firefox，Safari和Edge可获得最佳用户体验，但为较旧的浏览器或禁用了JavaScript会提供静态回退。


## 特征

有很多可选的扩展名和配置选项，用于自定义目录列表的Web外观。所有标记都是有效的HTML5，并结合了CSS3和最优质的JavaScript，可建立一个新颖但最少的用户界面，并专注于文件的用户体验。

一些可选功能包括：文件排序，不同的视图模式，本地化，面包屑，树视图，自定义页眉和页脚，文件过滤器和搜索，文件夹大小，自动刷新，打包下载，QR码，缩略图，文件预览


## 安装

1. 将文件夹复制 `_h5ai` 到Web服务器的文档根目录： `DOC_ROOT/_h5ai` 。

```
DOC_ROOT
 ├─ _h5ai
 ├─ your files
 └─ and folders
```

2. 问 `http://YOUR-DOMAIN.TLD/_h5ai/public/index.php` ，检查h5ai是否可以访问。此页面显示有关服务器功能的一些提示。

3. `/_h5ai/public/index.php` 在默认索引文件列表的末尾添加（请注意斜杠！）。通过这种方式， `h5ai` 将管理该目录内和目录下所 `有DOC_ROOT` 没有索引文件的目录。

Apache httpd 2.2 / 2.4：例如 `httpd.conf` 在根目录的 `.htaccess` 文件集中或在根目录中：

```
DirectoryIndex  index.html  index.php  /_h5ai/public/index.php
```

lighttpd 1.4：`lighttpd.conf` 例如：

```
index-file.names += ("index.html", "index.php", "/_h5ai/public/index.php")
```

nginx 1.2：`nginx.conf` 例如，在集合中：

```
index  index.html  index.php  /_h5ai/public/index.php;
```

切诺基1.2：`cherokee.conf` 例如：

```
vserver!1!directory_index = index.html,index.php,/_h5ai/public/index.php
```

## 组态

主要配置文件是 `_h5ai/private/conf/options.json` 。您可能要更改某些记录的设置。但是，`_h5ai/private/conf` 您可能需要查看更多文件。

## 提示

不支持Web服务器特定的功能，包括访问限制！使限制区域正常工作和保护的最佳机会可能是将文件夹_h5ai完全放在受限制的区域内。需要您自担风险使用它！

通常不适用于别名文件夹（在Apache httpd中可用）。别名文件夹使不可能将URL映射到文件系统文件夹。

如果未显示任何图标，则可能是您必须将SVG MIME类型添加到服务器。

在Ubuntu服务器上，您可能需要安装其他软件包以支持PHP JSON。

要使用基于shell命令的可选功能exec，passthru必须使用PHP功能，并且不能在中禁用PHP功能php.ini（请参阅参考资料disable_functions）。

v0.22.0-v0.24.1版本中存在一个安全漏洞，该漏洞已在v0.25.0 中修复（2014年夏季）。如果您仍在使用这些版本之一，则建议升级。

## 自定义安装

可以将h5ai安装到Web服务器文档根目录的任何子目录中。当显示面包屑等时，此目录将被视为根目录。

例如，将文件夹复制 `_h5ai` 到 `DOC_ROOT/some/folder/_h5ai` ：

```
DOC_ROOT
 └─ some
     └─ folder
         ├─ _h5ai
         ├─ your files
         └─ and folders
```
参观 `http://YOUR-DOMAIN.TLD/some/folder/_h5ai/public/index.php` ，看看是否一切正常。在此示例中，您需要添加 `/some/folder/_h5ai/public/index.php` 到索引文件列表中（如上面的步骤3所示）。

## 参考：
https://larsjung.de/h5ai/
