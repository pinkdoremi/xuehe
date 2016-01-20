title: webp初探
date: 2015-06-29 21:21:52
categories:
- 图片压缩
tags:
- 图片压缩
- webp
---

webp格式是目前各大厂商大热图片压缩格式。各种优势已经是路人皆知了。
最近测试使用了一下webp格式。
# webp介绍
这个其实不用多说了，就是一种图片格式。目前在移动端上只有安卓支持它，ios并不支持。
## 使用方法
在chrome上是兼容的。Safari和firefox不兼容。
具体的兼容性请查看[caniuse](http://caniuse.com/#feat=webp)
这里只介绍了在移动端上的情况。
<!--more-->
### 如果是在兼容它的Android上的话：
直接作为img标签或者作为background都可以。
### 非支持设备
如果设备不支持这个图片，那么直接写入在img或background中都是无法显示图片的。必须将图片转化成PNG后才能读取。
有两种将转化的思路
+ 使用js将图片转化为base64的PNG格式。
  目前已经有了转化的js库[http://libwebpjs.appspot.com/](http://libwebpjs.appspot.com/)，但是它的缺点是**只支持有损webp的转化**。此外它在**移动端上的处理速度非常慢**，很小的一张图都需要2-3秒以上，大图300k的大图甚至需要10s+。再来是这个解析库本身比较大（50KB+）,如果要加载的话成本略大。所以我认为在移动端上使用js库来进行转化是不可取的。
+ 让native提供转化的方法，让转化交给native来处理，将处理的结果传递给前端。
  我并没有做native的相关测试。但是目前很多使用了webp的公司在移动端上都使用了native做转化的方式去处理的。

### 使用情况分析
下面是适合的场景排序：
1. 仅仅在native中会出现的图片。全部使用webp格式，在ios中做native的PNG转化。
2. 在移动端各平台上（微信，UC等等）都会出现的图片 | 在pc端也会出现的图片。同时保存一份webp和原始格式。

如果是1的做法，那么省流量+省空间。
如果是2的做法，则省流量+费空间。
但是如果是CDN流量非常高的情况下，那么空间的代价应该比不上CDN的。

# webp压缩指令
我目前测试时使用的webp压缩的cli工具来自于谷歌。
# cli
webp转化的工具地址[https://developers.google.com/speed/webp/docs/precompiled](https://developers.google.com/speed/webp/docs/precompiled)
cli详细的使用方式见这里：[https://developers.google.com/speed/webp/docs/using](https://developers.google.com/speed/webp/docs/using)
在这里贴出简单的转化webp的方法，在命令行中键入：

    cwebp -q 80 image.png -o image.webp
    # 意思为将image.png有损压缩80品质输出位image.webp中。

如果是无损压缩的话，则—lossless即可。

# 压缩场景
在使用若干jpg,png进行试验后发现：
1. 相同质量的情况下，webp压缩的大小要比jpg,png小20%-30%；
2. 有损压缩优势明显，无损压缩0优势。
3. 压缩带透明度的png的时候竟然比原图还大。

所以综上所述，使用webp的场景应该是不透明的有损压缩。

# 兼容性测试
谷歌官方提供的兼容性测试的代码如下：

    // check_webp_feature:
    //   'feature' can be one of 'lossy', 'lossless', 'alpha' or 'animation'.
    //   'callback(feature, result)' will be passed back the detection result (in an asynchronous way!)
    function check_webp_feature(feature, callback) {
        var kTestImages = {
            lossy: "UklGRiIAAABXRUJQVlA4IBYAAAAwAQCdASoBAAEADsD+JaQAA3AAAAAA",
            lossless: "UklGRhoAAABXRUJQVlA4TA0AAAAvAAAAEAcQERGIiP4HAA==",
            alpha: "UklGRkoAAABXRUJQVlA4WAoAAAAQAAAAAAAAAAAAQUxQSAwAAAARBxAR/Q9ERP8DAABWUDggGAAAABQBAJ0BKgEAAQAAAP4AAA3AAP7mtQAAAA=="
            animation: "UklGRlIAAABXRUJQVlA4WAoAAAASAAAAAAAAAAAAQU5JTQYAAAD/////AABBTk1GJgAAAAAAAAAAAAAAAAAAAGQAAABWUDhMDQAAAC8AAAAQBxAREYiI/gcA"
        };
        var img = new Image();
        img.onload = function () {
            var result = (img.width > 0) && (img.height > 0);
            callback(feature, result);
        };
        img.onerror = function () {
            callback(feature, false);
        };
        img.src = "data:image/webp;base64," + kTestImages[feature];
    }

显然，这个兼容性的检查是异步的。不能在初始的时候就知道是否能够解析webp。
# 效率
解析时间：就webp格式的解析来说，以肉眼是感受不到差别的。斯认为没有必要去纠结这些略微多出来的解析时间。

# 参考
详细的数据参考：
[WebP 探寻之路](http://isux.tencent.com/introduction-of-webp.html)
[智图—源于QQ空间图片WebP化的思考](http://isux.tencent.com/zhitu.html)
