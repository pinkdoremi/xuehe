title: 细说PhantomJS
date: 2015-08-09 23:43:01
tags:
- phantomJS
- 爬虫
- 测试
categories:
- 工具
---
## [PhantomJs](http://phantomjs.org/)是什么？
Phantom JS是一个服务器端的 JavaScript API 的 WebKit。支持各种Web标准： DOM 处理, CSS 选择器, JSON, Canvas, 和 SVG。
## [PhantomJs](http://phantomjs.org/)干什么？
它可以用来模拟浏览器环境，实现不开浏览器隔空跑基于页面环境上的脚本。

### 使用方式
用phantomJs 写一个脚本，然后在命令行运行
### 具体的功能点：

#### [截屏](http://phantomjs.org/screen-capture.html)
因为phantomJs使用了webkit的内核，自带布局和渲染，所以是可以截屏的。如下：截图github。

      var page = require('webpage').create();
      page.open('http://github.com/', function() {
          page.render('github.png');
          phantom.exit();
      });


#### [网络监控](http://phantomjs.org/network-monitoring.html)
<!--more-->
phantomjs对于任何的请求和返回都能捕捉到，我们可以对其进行分析，篡改。

      var page = require('webpage').create();
      page.onResourceRequested = function(request) {
          console.log('Request ' + JSON.stringify(request, undefined, 4));
      };
      page.onResourceReceived = function(response) {
          console.log('Receive ' + JSON.stringify(response, undefined, 4));
      };
      page.open(url);

可以用YSlow这种性能检测工具与它[结合在一起](http://yslow.org/phantomjs/)，接入持续集成系统。

#### [页面自动化](http://phantomjs.org/page-automation.html)
其实就是进行js操作，可以操作dom，css这些，可以自定义userAgent。拥有这些功能就能当脚本跑了。

      var page = require('webpage').create();
      console.log('The default user agent is ' + page.settings.userAgent);
      page.settings.userAgent = 'SpecialAgent';
      page.open('http://www.httpuseragent.org', function(status) {
        if (status !== 'success') {
          console.log('Unable to access network');
        } else {
          var ua = page.evaluate(function() {
            return document.getElementById('myagent').textContent;
            });
            console.log(ua);
        }
        phantom.exit();
      });

此外，它还能引入别的脚本。比如引入JQuery操作DOM

      var page = require('webpage').create();
      page.open('http://www.sample.com', function() {
        page.includeJs("http://ajax.googleapis.com/ajax/libs/jquery/1.6.1/jquery.min.js", function() {
          page.evaluate(function() {
            $("button").click();
          });
          phantom.exit()
        });
      });

#### 其他API
#### # [fileSystem](http://phantomjs.org/api/fs/method/absolute.html)
支持文件操作。

    var fs = require('fs');

#### # [child_process](http://phantomjs.org/api/child_process/)
这点也类似node中的，可以执行命令行操作。

##### [system](http://phantomjs.org/api/system/)
获取变量，环境变量，以及一些系统信息。

##### [webserver](http://phantomjs.org/api/webserver/)
支持开启服务器，监听，关闭。


### 实际运用：
#### 测试
phantomjs本身不是测试框架，它可以结合测试框架进行测试。让一些js测试框架，除了能测试js代码，还能够模拟浏览器交互，测试输出。
官网有目前测试框架中已经结合了phantomjs的[列表](http://phantomjs.org/headless-testing.html)

我们可以实现，模拟交互，验证输出等操作。实现BDD，或者持续集成系统中的测试环节。
#### 爬虫
比如说抓取网站信息到本地生成一个文件，或者爬数据等各种爬虫脚本都可以写。参见[利用phantomJs制作的爬股票脚本](http://yukihe.com/2015/07/02/%E5%88%A9%E7%94%A8phantomJs%E5%88%B6%E4%BD%9C%E7%9A%84%E7%88%AC%E8%82%A1%E7%A5%A8%E8%84%9A%E6%9C%AC/)

### 相似工具
#### [SlimerJS](http://slimerjs.org/)
和PhantomJS的各方面功能都差不多。细看文档非常相似。但是他是基于Gecko的。
#### [trifleJS](http://triflejs.org/)
同上之ie版。
### 衍生工具
#### [phantomCSS](https://github.com/Huddle/PhantomCSS)
界面测试工具。是使用的像素对比的方式。像素对比使用了[Resemble.js](http://huddle.github.io/Resemble.js/)
#### [BackstopJS](http://garris.github.io/BackstopJS/)
响应式界面的像素对比。像素对比使用了[Resemble.js](http://huddle.github.io/Resemble.js/)
#### [casperjs](http://casperjs.readthedocs.org/en/latest/)
PhantomJS和SlimerJS的合体加强版
#### [phantomas](https://github.com/macbre/phantomas)
基于phantomJS的性能测试工具。他能测试页面加载时间、页面请求数、资源大小、是否开启缓存和Gzip、选择器性能、dom结构等性能指标。
## 参考
+ [前端自动化测试探索](http://fex.baidu.com/blog/2015/07/front-end-test/)
