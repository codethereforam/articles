# 完善chrome翻译插件ChaZD，支持有道智云api

首先放上该项目的github地址: https://github.com/codethereforam/ChaZD

之前想找一个chrome支持划词翻译的插件，最终在知乎上看到了[这个回答](https://www.zhihu.com/question/20158063/answer/28195077)，推荐的是[ChaZD](https://github.com/ververcpp/ChaZD)，用了一段时间我就喜欢上它的简洁方便。

这个插件用的是有道翻译旧的api，由于有道限制每个api key每小时只能调用1000次，有时候会翻译不了，而且旧的api于 2017-12-31 后停止运行。

于是，我就改写了这个插件，使其可以调用有道智云新的api。我fork的是[lytofb/ChaZD](https://github.com/lytofb/ChaZD)，作者在原来ChaZD代码的基础上加了自定义api key的功能。

如果大家在使用过程中遇到问题或者发现bug，欢迎在项目issues提出，也欢迎大家star、fork。
 
## 一、版本介绍

如果你想体验原版的ChaZD，请选择v1.0.0，后面几个版本是我根据个人的使用情况和偏好添加了几个功能，欢迎尝试。

### v1.0.0

- 改用有道智云api
- 可以自定义应用ID和密钥

### v1.1.0

- 支持中译英

### v1.2.0

- 划词翻译模式添加网络释义

### v1.2.1
- 修复网络释义字体异常
- 修复无法翻译句子
- popup翻译模式响应事件改为enter


## 二、使用说明

### 2.1 下载插件

下载地址：https://github.com/codethereforam/ChaZD/releases

选择你想要的版本，下载ChaZD-thinkam.crx文件。如果你的浏览器支持使用非Web Store的插件，直接将下载的crx拖到chrome://extensions/页面。

如果不支持，将crx文件扩展名改为zip并解压，在chrome://extensions/勾选开发者模式，接着在 Chrome 设置拓展的地方，点击加载未打包的拓展。详细步骤见[chrome怎么安装非官方市场的插件？ - 小赖的回答](https://www.zhihu.com/question/24027794/answer/34500157)。

### 2.2 申请有道智云翻译服务

- 注册[有道智云](http://ai.youdao.com/)帐号并登录到控制台页面
- 创建一个翻译实例：控制台 > 自然语言翻译 > 翻译实例 > 创建翻译实例
- 创建一个应用并绑定翻译服务：控制台 > 应用管理 > 我的应用 > 创建应用 > 绑定服务
- 在插件中绑定应用——将上步骤的应用ID和应用密钥填写到插件设置页中相应的位置，如下图

![](/images/2017-12-31-ChaZD-youdao-01.png)

### 2.3 具体翻译说明

参考项目README: https://github.com/codethereforam/ChaZD/blob/master/README.md 

## 三、结尾
 
有道翻译旧的api于明天就停止运行了，原来的这个插件应该就不能用了，我赶在前一天完善了一下这个插件，之前的老用户可以继续使用。如果你没用过，欢迎尝试。

由于时间仓促加上我刚接触插件才两天，代码应该还有一些问题，欢迎提出问题。后面我会继续完善代码，添加一些定制功能。

 
