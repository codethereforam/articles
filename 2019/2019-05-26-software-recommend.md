# Windows、Linux、Android常用软件分享

## 前言
本来没准备写这篇博客，一是没时间，还有其他很多优先级更高的事情要做。二是写这种博客对我自己来说没什么的帮助，以前我就想好了不写教程类，使用类的博客。本来只是打算在Gist记录下，没准备发博客，后来想到这可能会帮助到一些人，就稍微多花一点时间写一写。这篇文章的目的主要是分享一些我用过的软件中我觉得优秀的、安全的软件。

> 当然我不是在这里打击 Linux 和 Mac 而鼓吹 Windows。这些系统的纷争基本上已经不关我什么事。我只是想告诉新人们，去除头脑里的宗教，偏激，仇恨和鄙视。每一次仇恨一个东西，你就失去了向它学习的机会。 —— 王垠

先引用王垠一句话表明我的态度，我非常赞成他的看法。对待技术，对待软件，不要有傲慢与偏见，对于同类的东西A和B，不要因为用了A就排斥和贬低B，A和B可能各有所长，各有各的适用场景，有时候可以多者结合着使用。

举个例子，对于操作系统，以前大概有一年多的时间我只使用Ubuntu，觉得用Linux很geek，Windows就是垃圾，只有实在没办法的情况（比如QQ接收别人发的文件）才会用虚拟机打开一个Windows系统用一会。后来由于工作原因，平时大部分时间用的都是Windows，我就在自己的新电脑上就用了预装的Windows，win10加上其子系统可以很好地完成平时的工作以及娱乐，子系统我装的是Ubuntu，我使用命令行工具Conemu输入bash命令进入子系统。然后我想通了一个问题，我觉得Linux好主要是需要它的内核，而不是它的图形界面，而win10加上其子系统基本上满足了我的需要，所以对我来说用什么系统就无所谓了。

再举一个例子，对于学某个知识，有时不要只局限于一本书，可以多者结合者看。比如在选择Java并发的书籍时，《Java并发编程实战》的口碑远高于《Java并发编程的艺术》，根据我的经验，可以以《Java并发编程实战》为主，对于有些觉得没看懂的或者没有例子的可以去《Java并发编程的艺术》中找到相应的章节看一看。如果还是不是很懂，可以再去网上找一些文章看看。

下面进入正题，从几个维度谈一谈我选择软件的优先级：
- 开源软件 > 闭源绿色软件（无需安装，也无需依赖管理员权限的软件） > 闭源非绿色软件
- 非商业（非盈利性） > 商业（盈利性）
- 国外 > 国内

> 注：
> -  标记为R的是我平时优先使用的软件，有时我可能多个同类软件混用，各取所长。
> - 没有特殊说明，以下都是我用过的软件。
> - 下文中的跨平台是指Windows、Linux、macOS中都有的。

## 杀毒软件
- 如果是使用Linux，无需杀毒软件
- win10自带的Windows Definder（R; win10; 我觉得win10不需要其他第三方杀毒软件）

## 输入法
- Rime（R; 跨平台; 开源）
- Gboard（R; Android、IOS）

## 浏览器
- Firefox（R; 跨平台、Android、IOS; 开源）
- Chrome（跨平台、Android、IOS）
- Chromium（跨平台; 开源）

## 视频播放器
- vlc（R; 跨平台、Android、IOS; 开源; 我Android和Uuntu用这个）
- PotPlayer（R; Windows; 我Windows用这个）
  
## 压缩工具
- 7-Zip（R; 开源; windows）
- Windows自带（R; 我用来压缩解压zip格式）
- unar（R; Linux下的解压神器，支持zip、tar等格式，解压中文zip不会乱码）
- zip、unzip(R; Linux)
- tar(R; Linux)

## 截图工具
- Snipaste(R; 绿色软件; Windows、mac)
- [Flameshot](https://github.com/lupoDharkael/flameshot)（R; 开源; Linux）

## 邮件类
- Thunderbird（R; 跨平台; 开源; 可装插件支持markdown）
- Gmail（R; Android、网页版）
  
## 文本编辑器
- vscode（R; 开源; 跨平台; 也是我用的Markdown编辑、阅读器）
- vim（R; 开源; 跨平台）
- NotePad++(开源; 比vscode轻量)

## 多格式阅读器
- Calibre（R; 开源; 跨平台; 功能强大，有编辑、查看等功能，支持大多数的电子书格式）
- 静读天下(R; Android；支持大多数的电子书格式)

## PDF阅读器
- SumatraPDF（R; 开源; Windows; 还支持epub、mobi）
- Evince（R; 开源; Linux; Ubuntu自带）
- Foxit Reader（跨平台 、Android、IOS; 绿色软件）

## epub阅读器
- Microsoft Edge(R; Windows; 还支持PDF)
- Calibre（R; 我Ubuntu用这个）

## mobi阅读器
- Calibre（R）
  
## 网盘类
- Dropbox（R; 跨平台、Android、IOS; 支持差分同步，意思是只同步变动的部分）
- Google Drive（R; 容量大，我把不常用的放这）
- Nextcloud（R; 开源; 服务器搭建的私有网盘）

## 笔记类
- Google Keep（R; app、网页版）
- Gist网页版 + Lepton（R; 跨平台; 参考我的博客[ Gist使用经验 ](https://www.cnblogs.com/thinkam/p/10922920.html)）
  
## 命令行终端
- conemu（R; 开源; Windows）
- Terminal（开源; Windows; 目前还未发布正式版）

## 远程协助
- TeamViewer（R; 跨平台; 不安装，选择仅运行模式运行）
- Windows自带的（没用过，应该比TeamViewer更安全）

> 注：我平时基本不用这类软件

## 聊天类
- Telegram(R; 客户端开源; 跨平台、Android、IOS)
- Signal（R; 开源; 跨平台、Android、IOS）
  
> 注：我此类软件用的少，除此外还有很多优秀的并且更好的软件

## 浏览器扩展
- [better-onetab](https://github.com/cnwangjie/better-onetab)（R; 开源;Firefox、Chrome; 标签页储存和标签页分组）
- [personal-blocklist](https://github.com/wildskyf/personal-blocklist)（R; firefox版开源; Firefox、Chrome; Google搜索屏蔽指定的网站）
- [SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega)（R; 开源; Firefox、Chrome; 网络代理管理）
- [stylus](https://github.com/stylus/stylus)（R; 开源; Firefox、Chrome; 定制网页样式）
- [ublock-origin](https://github.com/gorhill/uBlock)（R; 开源; Firefox、Chrome; 广告过滤、禁用某个网站JS等功能）
- [沙拉查词](https://github.com/crimx/ext-saladict)（R; 开源; Firefox、Chrome; 划词翻译）
- [greasemonkey](https://github.com/greasemonkey/greasemonkey)（R; 开源; Firefox; 定制网页JS脚本，Chrome有替代品Tampermonkey）
- [New-Tongwentang-for-Firefox](https://github.com/tongwentang/New-Tongwentang-for-Firefox)（R; 开源; Firefox; 简繁转换）
- [gist-markdown-preview](https://github.com/revathskumar/gist-markdown-preview)（R; 开源; Firefox、Chrome; Gist编辑markdown预览）
- [octotree](https://github.com/ovity/octotree)（R; 开源; Firefox、Chrome; GitHub文件目录树）

## 其他
- Inoreader（R; 网页版、app; RSS阅读器）
- 7+ Taskbar Tweaker(R; Windows; 缩小window任务栏高度，还有其他功能)
- Easy Window Switcher（R; Windows; 快速在同一个程序的不同窗口中切换，模仿Ubuntu中ctrl + '`'的功能）
- PDF书签制作工具：参考[我的GitHub](https://github.com/codethereforam/pdf-bookmarks#%E5%A6%82%E4%BD%95%E5%88%B6%E4%BD%9C%E5%B8%A6%E4%B9%A6%E7%AD%BE%E7%9A%84pdf%E7%94%B5%E5%AD%90%E4%B9%A6)
-  Virtual Box（R; 开源; 跨平台；虚拟机）

> 以下是专业类软件

----

## 代码比较工具
- IDE和文本编辑器自带的（R; 比如IDEA和vscode都有文本或文件比较功能）
- WinMerge(R; 开源)

## 接口测试工具
- IntelliJ IDEA中的Rest Client(R)
- httpie(R; 开源; 跨平台)
- curl(R; 开源; 跨平台)

## 数据库工具
- DataGrip(R; 智能提示比Navicat优秀)
- Navicat(R; 有一些DataGrip没有的功能，比如导入Excel)

## 抓包工具
- Burp Suite社区版（R; 跨平台; 抓HTTP包; 此外还有很多其他渗透测试功能）
- Wireshark（R; 开源; 跨平台; 抓传输层的包）

## 写这篇博客用的软件
我用vscode写的这篇文章，用的markdown格式，vscode支持markdown实时预览。写完后用Git上传到[我的GitHub仓库](https://github.com/codethereforam/articles)，既可版本控制，又可备份，避免了单点故障。最后，发布到博客平台。
