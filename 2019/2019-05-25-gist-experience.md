## Gist使用经验

> 注：本文只是分享Gist使用经验，不讨论类似软件或服务的优劣，对于技术或软件不要有傲慢与偏见

## 一、Gist是什么
关于Gist的详细介绍，请阅读官方文档[About gists](https://help.github.com/en/articles/about-gists)，下面只简略介绍我所用到的功能：
- Gist可以用来写一些东西，然后可以分享，类似笔记软件
- 每个Gist都是一个Git库，有版本历史，可以被fork或clone
- Gist有两种：公开的和私有的，私有的不会在你的Gist主页显示，也无法用搜索引擎搜索到，但这个链接是人人都能访问的
- Gist可以搜索、下载、嵌入到网页

## 二、我为什么不使用笔记软件
因为我平时很少记录或写东西，Gist对我来说已经够用了，而且Gist有Git的功能，我觉得没必要尝试其他软件。至于备忘，我会写到Google Keep或者桌面上的一个'todo.md'的文件

## 三、我用Gist做什么
1. 保存代码片段：平时写代码时觉得写的比较好的代码片段（比如工具类或其中的方法），我会在IDEA中右键创建Gist，方便以后在其他地方写代码时快速查找
2. 保存配置：创建一个私有的Gist，保存一些软件（比如浏览器扩展）的配置，方便在用其他电脑时同步
3. 记笔记：对于一些重要的知识点，搜集资料后整理出来，以后可以分享给别人或和别人讨论时拿出来证明自己的观点
4. 记录简短的想法或总结：有时候想总结一些技术或经验，或者有一些想法，由于内容比较短，还不足以发表博客，可以先记录下来

## 四、浏览Gist
由于网页上的Gist没有目录导航，我觉得翻阅以前写过的Gist不方便，所以我有时会使用[Lepton](https://github.com/hackjutsu/Lepton)浏览Gist

## 五、如何创建、编辑Gist
1. 一些IDE或者文本编辑器的插件有创建Gist的功能，比如在IDEA中右键选择'Create Gist...'创建Gist，vscode也有Gist插件
2. 如果不是markdown格式，可以使用网页或者Lepton
3. 如果是markdown格式，浏览器安装[gist-markdown-preview](https://github.com/revathskumar/gist-markdown-preview)扩展，页面上创建、编辑Gist，使用扩展预览markdown，而Lepton无法预览markdown

## 六、备份Gist
如果你足够信任GitHub的服务，可以不做这一步。但为了防止单点故障，万一GitHub服务器数据都没了，本地还有一个备份。对于网络服务，我一般本地还会保存一份。

我只在Ubuntu和Win10的Ubuntu子系统试过，所以Win10子系统、Linux、Mac应该都可行。

安装开源软件[gister](https://github.com/weakish/gister)，该软件依赖[gist](https://github.com/defunkt/gist)，按照REAMDE安装这两个，此外还依赖curl、git、jq。下面列举一些要注意的东西：
- 要先初始化好Git，然后上传公钥，要确保能通过ssh访问GitHub
- 若Gist无法访问，可以安装[proxychains-ng](https://github.com/rofl0r/proxychains-ng)，使用代理执行命令

> 最后，附上我的Gist地址：[codethereforam's gists](https://gist.github.com/codethereforam/)
