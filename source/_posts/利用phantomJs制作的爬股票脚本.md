title: 利用phantomJs制作的爬股票脚本
date: 2015-07-02 22:41:48
tags:
- phantomJs
categories:
- 爬虫
---
思路：
1. 利用phantomJs爬取同花顺网站。
2. 利用选择器找到网站上的价格数据。
3. 利用每隔一秒重新获取价格的方式拿到实时的价格。
4. 在命令行显示一个界面显示价格。

大概就是这样。一边写一边来吧。

#phantomJs
目前我只知道phantomJs是干嘛用的：就是提供一个模拟浏览器的环境。
然后去看看怎么用这个玩意。
<!--more-->
##安装
官方的网址->[http://phantomjs.org/build.html](http://phantomjs.org/build.html)
然后根据教程来进行安装，本来安装步骤是不打算写的，但是没想到今天网不太好，git下载了好久都没有下载好。
###安装步骤：
    git clone git://github.com/ariya/phantomjs.git
    cd phantomjs
    git checkout 2.0
    ./build.sh --qt-config "-I /usr/local/include/ -L /usr/local/lib/"
......等了很久还没有下载完。
于是看到了这里：[download](http://phantomjs.org/download.html)
直接下载了zip包.=……=
最终获得是是`bin/phantomjs.exe`这个文件。
###安装方法2
还有个办法是使用npm或者brew安装，就不细说了，大家应该都懂。
###安装方法3
淘宝镜像有[下载地址](http://npm.taobao.org/mirrors/phantomjs)
#编写脚本
ok，接下来进行脚本的编写啦。
我建立一个名为`run.js`的脚本。
恰巧看到start中有个类似的demo是这样的。

    var page = require('webpage').create();
    page.open(url, function(status) {
      var title = page.evaluate(function() {
        return document.title;
      });
      console.log('Page title is ' + title);
      phantom.exit();
    });
貌似是一个打开一个页面后获得页面的标题的作用。那我只要改改这个demo，不用获得标题，只需要查找页面的一个价格元素就可以了。
接着我找到了雪球网的网址[http://xueqiu.com/S/SH000001](http://xueqiu.com/S/SH000001)
显然这个域名后面跟着#的就是股票代码了。接着我替换url：

    var page = require('webpage').create();
    var code = 'SH000001';
    page.open('http://xueqiu.com/S/'+code, function(status) {
      var title = page.evaluate(function() {
        return document.title;
      });
      console.log('Page title is ' + title);
      phantom.exit();
    });
接下来去雪球的网站上找到价格的选择器:`.currentInfo .stockDown`,这个选择器能选到两个元素，包含了当前的价格，当前的波动幅度等重要数据。修改代码：

    var page = require('webpage').create();
    var code = 'SH000001';
    page.open('http://xueqiu.com/S/'+code, function(status) {
      var price = page.evaluate(function() {
        var res = '',
            doms = document.querySelectorAll('.currentInfo');
        [].forEach.call(doms, function(item) {
            res += '\t' + item.textContent;
        });
        return res;
      });
      console.log('Price is ' + price);
      phantom.exit();
    });

启动却发现，doms里面什么元素都没有，我用`page.render`方法截了个图，发现403被阻止了。网上调查发现，爬虫脚本经常会出现这种问题。于是我改了一下请求的头部信息，模拟成真实数据的请求：

    page.onResourceRequested = function(requestData, networkRequest) {
        networkRequest.setHeader('User-Agent',
            'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/43.0.2357.130 Safari/537.36'
        );
    };
此时，我发现选择器拿到的文本在windows环境下出现了乱码，于是我要筛选出需要的数据：

    res = res.replace(/[^0-9.+\-%\s\(\)]/g, '');
下面，把股票的代码写活了，通过命令行参数传递，默认上证指数。

    var page = require('webpage').create(),
        system = require('system'),
        code = encodeURIComponent(system.args[1] || ''),
        url = 'http://xueqiu.com/S/' + (code || 'SH000001');
好了，最后把代码整合一下，做一下`setInterval`每隔一秒数据刷新，最终的代码是这样的：

    var page = require('webpage').create(),
        system = require('system'),
        code = encodeURIComponent(system.args[1] || ''),
        url = 'http://xueqiu.com/S/' + (code || 'SH000001');
    page.onResourceRequested = function(requestData, networkRequest) {
        networkRequest.setHeader('User-Agent',
            'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/43.0.2357.130 Safari/537.36'
        );
    };
    page.open(url, function(status) {
        if(status !== 'success') {
            return;
        }
        console.log('NowPrice is:' + page.evaluate(getLatestPrice));
        setInterval(function() {
            console.log('NowPrice is:' + page.evaluate(
                getLatestPrice));
        }, 1000)
    });

    function getLatestPrice() {
        var res = '';
        var doms = document.querySelectorAll('.currentInfo');
        [].forEach.call(doms, function(item) {
            res += '\t' + item.textContent;
        });
        res = res.replace(/[^0-9.+\-%\s\(\)]/g, '');
        return res;
    }

人生第一个爬虫脚本做好啦，开心~！简单实用，够我好好用了。。

7月9日更新
-----------------------
猛然有一天发现phantomjs也被fiddler抓到了请求。

猛然发现。股票数据请求在一条一条地发。。。

猛然发现真是什么数据都有。。。。

好吧，这篇文章就是phantomjs入门。。。等我空了修改修改爬股股脚本再共享出来。。
