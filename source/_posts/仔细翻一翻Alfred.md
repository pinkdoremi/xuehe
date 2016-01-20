title: 仔细翻一翻alfred
date: 2015-10-11 04:13:45
tags:
- alfred
categories:
- 工具
---
记得自己很久以前刚用mac的时候下了个alfred但是一直没好好学着怎么用，放着很久压箱底，整理软件的时候把它删掉了，最近在公司看到同事们的alfred都玩得好溜，决定重新补一下alfred。
### 关于收费
它目前v2版本的收费是这样子的：

+ 单台机子 17欧
+ 5台机子 family版 27欧
+ 终生可升级版&单台机子(意思是出现v3版本的时候不用再买了) 32欧

我趁着现在欧元汇率很低的情况下(写的时候1:7)赶紧买了个27欧版本。想来自己和男票的电脑数量凑凑有4台，家庭版比较合算。
### 同步设置
官方同步教程：[link](https://www.alfredapp.com/help/advanced/sync/)

如果自己有多台设备的话，同步功能必不可少，省的一台一台电脑去设置啦。

<!-- more -->

首先看看有哪些可以同步：
+ 快捷键
+ 主题
+ 朗读语言
+ 自定义搜索
+ 历史设置以及历史数据
+ 自定义文件系统导航的目录
+ 1Password enabled and keychain location

#### 同步步骤
1. 首先得有个网盘。百度云,dropbox,icloud Drive都可以。因为它的同步是把配置文件放在一个文件夹里，不同的电脑共享这个文件夹来实现同步设置的。如果没有网盘的话，就不能同步得很及时，需要手动去同步。
2. 在`Advanced`-`Syncing`-`Set Sync Folder`中设置一下同步的文件夹。设置完毕后Alfred会重新启动一下。这样就可以同步了！

# 功能介绍
## 字典
`define + 空格 + 单词`可以吊起对这个词语的字典搜索。但是相比mac自己的三只手指吊起字典来说，还是后者更方便。

## [Packal](http://www.packal.org/)
这是一个提供`Alfred`的`Workflow`和`Theme`的平台。里面有好多的资源可以下载。

## 外貌协会必看
改主题肯定还是从Packal中去装主题，此外，还可以通过Appearance中的window blur去让Alfred遮挡的部分的背景模糊掉。如果是用的透明度主题的话这个一定要设置一下，否则背景上的东西容易看不清Alfred上的东西。
![blur 100%的效果](/img/blur.jpeg)

## 设置
个人认为设置默认输入法是英文输入很有必要。。。因为输入的keyword大多数都是英文的。。。后面才有可能是中文呀。。。在Advanced中设置输入法吧~


## Web Search

`Features->Web Search`。Web Search包括两块内容：

+ 另一个则是输入地址会自动识别出是个网址，可以跳转到那个页面。比如输入`weibo.com`可以跳转到`http://weibo.com`。

![go_to_weibo.com](/img/redirect.jpeg)

+ 一个是自定义搜索，输入一个指令，可以跳转到带关键词的搜索结果页面`google apple`，或者是直接跳转一个直接的网址。

![googleapple](/img/google_apple.jpeg)

![weibo](/img/go_weibo.jpeg)

前者的跳转历史会被自动记录下来,下次无须输入完整的地址，系统会联想到之前跳转的链接。

而后者的功能就相对来说比前者要复杂，而且可以自己定制，比较有意思。下面着重介绍这块功能。

### 自带的搜索
`Alfred`自带的搜索很多并且有些功能非常不错，我整理保留了一些，也关闭了一些不会用到的。
推荐介绍几个：
#### maps
maps可以唤起2个地图，一个是谷歌的，还有个是苹果自带的。我想再加个百度的但是没找到百度的地址该怎么加。
#### translate
谷歌翻译。可以直接输入要翻译的内容。
#### movie
内置两个搜电影的，一个是烂番茄[Rotten Tomatoes]，一个是IMDB，建议加个豆瓣或者时光网。统一将`keyword`设置为`movie`.
#### 搜索，wiki
`lucky`搜索直接跳到结果页面的方式对于打开官方网站的方式来说很快捷。比如`lucky 英雄联盟`就会直接跳到`http://lol.qq.com/`。

而他自带的谷歌搜索不用多介绍，不输入google，直接输入关键字都能够去搜索，wiki百科也是一样。

推荐个萌萌哒duckduckgo，旨在无痕搜索，不会记录任何搜索记录的。keyword是duck。

### 自定义搜索
添加自定义搜索渠道的入口是`Features`->`Web Search`->`Add Custom Search`

在`Search URL`中添加搜索引擎的网址,其中用`{query}`表示搜索关键词，搜索的时候会替换成关键字。验证搜索地址正确的方式是设置面板的下面有个测试，输入关键字点`Test`就能看到效果了。

`Title`填写你希望的提示语句。建议都用类似于`Search Taobao for '{query}'`这种话。

`Keyword`表示唤起搜索时输入的词语。

如果比较闲的话，最好再去网上搜个搜索引擎的logo下载到本地，拖到边上的方框里面，作为搜索引擎的标识比较容易观察。


#### 下面介绍我自己写的几个自定义搜索：

|搜索|Alfred配置(点击自动会配置)|keyword|
|:------------:|:------------:|:------------:|
|淘宝|[点我](alfred://customsearch/Search%20Taobao%20for%20%27%7Bquery%7D%27/buy/utf8/noplus/http://s.taobao.com/search?q={query})|buy|
|天猫|[点我](alfred://customsearch/Search%20Tmall%20for%20%27%7Bquery%7D/buy/utf8/noplus/https://list.tmall.com/search_product.htm?q={query})|buy|
|京东|[点我](alfred://customsearch/Search%20JD%20for%20%27%7Bquery%7D%27/buy/utf8/noplus/http://search.jd.com/Search?keyword={query})|buy|
|聚美优品|[点我](alfred://customsearch/Search%20Jumei%20for%20%27%7Bquery%7D%27/buy/utf8/noplus/http://search.jumei.com/?search={query})|buy|
|考拉海购|[点我](alfred://customsearch/Search%20Kaola%20for%20%27%7Bquery%7D%27/buy/utf8/noplus/http://www.kaola.com/search.html?key={query})|buy|
|easyIcon|[点我](alfred://customsearch/Search%20EasyIcon%20for%20%27%7Bquery%7D%27/icon/utf8/noplus/http://www.easyicon.net/iconsearch/{query}/)|icon|
|weibo搜索|[点我](alfred://customsearch/Search%20Weibo%20for%20%27%7Bquery%7D%27/weibo/utf8/noplus/http://s.weibo.com/weibo/{query})|weibo|
|豆瓣电影|[点我](alfred://customsearch/Search%20Douban%20Movie%20for%20%27%7Bquery%7D%27/movie/utf8/noplus/http://movie.douban.com/subject_search?search_text={query})|movie|
|豆瓣FM|[点我](alfred://customsearch/Play%20Music%20Now%21/music/utf8/noplus/http://douban.fm/)|music|
|打开微博|[点我](alfred://customsearch/Open%20Weibo/weibo/utf8/noplus/http://www.weibo.com/)|weibo|
|知乎|[点我](alfred://customsearch/Search%20Zhihu%20for%20%27%7Bquery%7D%27/zhihu/utf8/noplus/http://www.zhihu.com/search?q={query})|zhihu|
|caniusr|[点我](alfred://customsearch/Search%20CanIuse%20for%20%27%7Bquery%7D%27/caniuse/utf8/noplus/http://caniuse.com/#search={query})|caniuse|
## 快速计算器
![计算器](/img/calculator.jpeg)
直接在Alfred中输入算式就会自动检测为计算器模式，下方就会出现计算结果，这比打开计算器软件，再算要方便多了。

如果输入算式结束后输入`=`号，算术结果会替代之前输入的算式，可以继续计算。
## 通讯录
输入通讯录朋友的名字例如`张三`就能自动调出朋友的电话邮箱。可以直接复制信息。我们也可以自定义关于这个用户的功能，qq啊，地址啊，网站等通讯录的信息。在`Features->Contacts->Custom Actions->'+'`中设置。

此外，可以`email + 名字`的方式直接调起向某人发邮件的功能。
## 剪贴板
alfred可以保存剪贴板的内容。默认是关闭的，需要手动打开。`Feature`-`Clipboard`，在这里，可以设置剪切板内容的保存时长。
使用`option+cmd+c`,或者输入`clipboard`都可以打开剪切板内容。
![weibo](/img/clipboard.jpeg)
它只会保存文字内容，不会保存图片，文件等。此外，它会显示剪切内容生产自哪个应用。

它还拥有删除记录`clear`功能，可以选择删除所有，5分钟内或者15分钟内的内容。
![weibo](/img/clear.jpeg)
为了保护用户的隐私和安全，它还可以设置一些不记录剪切板内容的应用，它内置了一些钱包，钥匙串应用在这个名单之内，用户可以自己控制在这个列表中添加、删除应用。
![weibo](/img/ignoreclip.png)
此外，用户可以添加自定义的内容作为`snippets`，把经常要用到的内容存放在这个功能中，用起来会非常方便。
## iTunes
Alfred自带一个小播放器界面，并且可以快捷控制iTunes的播放，但是我不用itunes，这个部分内容就没有自己再去看和试用,大家有兴趣可以自己研究一下这里面的快捷`keyword`。
![weibo](/img/iTunes.jpeg)
## 系统功能
Alfred的一些keywords直接调用系统的功能：

+ screensaver 屏保
+ trash 垃圾桶
+ empty trash 清空垃圾桶
+ logout sleep restart shutdown ....

等等功能，详细可以自己设置，还可以设置是否请求确认。

## Terminal
输入`>`后可以**直接输入命令行**指令，但是当前目录是在根目录的。此外，它还支持**自定义终端**。
## remote
Alfred支持手机来简单操作电脑上的内容，但是个人觉得操作重启关机，打开应用，打开书签，文件夹啥意义不是很大，况且手机上的这个软件还要花30大洋去装。。。我穷没买。。。
## 文件夹操作
输入`'`或者`空格`或者`open`可以直接打开文件，再输入2个空格，可以访问到最近的文件。
使用`find`可以打开文件的目录。

`in`可以打开带有关键字内容的文件。
## workflow
最有趣的部分就是这个了，知乎上有一个集合了很多不错的workflow的[问题](http://www.zhihu.com/question/20656680).我就不贴过来了，大家自己去看吧。自己用的时候可以自己给自己写一套combo。[packal](http://www.packal.org/)也提供了workflow下载。
---------
失眠把这篇文章写完了，总算困了，之后会收集一些好用的workflow补上。

