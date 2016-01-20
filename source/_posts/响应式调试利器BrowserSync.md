title: 响应式调试利器BrowserSync
date: 2015-06-26 20:24:05
tags:
- 调试工具
- browsersync
categories:
- 工具
---
# 用来干嘛的
[BrowserSync](http://www.browsersync.io/)可以监听你的页面文件的变化。每当页面文件变化时，所有访问该站点的设备都会同步刷新，并且显示最新的代码。
此外它还有个超级厉害的功能就是在任意一个设备上的操作，会同步到别的设备上，你下拉，别的页面也会下拉，你点按钮，别的页面也会点按钮。ui验收的时候，摆上一排机器，随便拉拉，页面都在晃，超级拉风的，而且对效率的提升非常高。
# 5分钟快速上手
1. 安装nodeJs
2. 安装BrowserSync
    ```        
    $ npm install -g browser-sync
    ```
3. 启动BrowserSync
    ```
    browser-sync start --server --files "css/*.css"
    ```
        每当`css/*.css`中的文件作出改动时，所有正在打开刚网站的设备都会获得最新的资源刷新页面。
<!--more-->
4. 命令行会打印出如下的内容
    ```
    [BS] Access URLs:
    ------------------------------------
           Local:http://localhost:3000
        External:http://10.1.201.36:3000
    ------------------------------------
              UI:http://localhost:3001
     UI External:http://10.1.201.36:3001
    ------------------------------------
    [BS] Serving files from: ./
    [BS] Watching files...
    ```
    其中上面的两个网站是你的网站
    下面的两个网站是可以提供UI控制。UI控制界面如下，可以自己多点点看。很有意思的。
![ui控制界面](/img/browsersync.bmp)

# 全部功能
+ 动作同步
同步click scroll form input&submit
+ 文件同步
同步文件变化，并且可以自己决定同步哪些文件，可以不用同步一些不想同步的文件。
+ 支持插件
在npm 中搜索browser sync plugin 可以找到browser sync 的插件，但是不多= =，
+ 远程调试的其他功能
    1. 自带远程调试利器weinre，比较方便不用自己再开weinre了，只要进UI控制的地方开个开关就好了。
    2. 还有两个功能就是为所有元素增加外观，用于看清页面布局。甚至可以带阴影，用于看清层级！！！
    3. 此外，还能增加网格，看元素对齐！！！
+ UI控制，CLI控制
+ 支持Grunt&Gulp
+ 提供网速模拟。可以限速。

# BrowserSync命令行帮助
自行体会：

        $ browser-sync -h
          Live CSS Reload & Browser Syncing
        
          Usage:
          ---------
        
              $ browser-sync <command> [options]
        
          Commands:
          ---------
        
              init    Creates a default config file
              start   Start Browser Sync
        
          Options:
          --------
        
              --no-reload-on-restart      Don't auto-reload all browsers following a restart
              --no-inject-changes         Reload on every file change
              --no-ghost-mode             Disable Ghost Mode
              --extensions                Specify file extension fallbacks
              --no-online                 Force offline usage
              --startPath                 Specify the start path for the opened browser
              --directory                 Show a directory listing for the server
              --no-notify                 Disable the notify element in browsers
              --logLevel                  Set the logger output level (silent, info or debug)
              --no-open                   Don't open a new browser window
              --ui-port                   Specify a port for the UI to use
              --exclude                   File patterns to ignore
              --version                   utput the version number
              --config                    Specify a path to a bs-config.js file
              --server                    Run a Local server (uses your cwd as the web root)
              --tunnel                    Use a public URL
              --files                     File paths to watch
              --proxy                     Proxy an existing server
              --https                     Enable SSL for local development
              --index                     Specify which file should be used as the index page
              --no-ui                     Don't start the user interface
              --host                      Specify a hostname to use
              --help                      Output usage information
              --port                      Specify a port to use
              --open                      Choose which URL is auto-opened (local, external or tunnel)
              --xip                       Use xip.io domain routing
        
        
          Server Example:
          ---------------
          Use current directory as root & watch CSS files
        
              $ browser-sync start --server --files="css/*.css"
        
          Proxy Example:
          ---------------
          Proxy `localhost:8080` & watch CSS/HTML files
        
              $ browser-sync start --proxy="localhost:8080" --files="*.html,css/*.css"

# 总结
真的好好用的呀！