Title: 一个用python写的命令行发微博工具开发记录
Date: 2014-1-27
Tags: python, 微博, project
Modified: 2014-1-27
Slug: weibo_in_line

#源起

一个常见的情景：工作中有感而发，想要吐槽两句，下意识的打开浏览器，进入新浪微博页面，然后就没有知觉了。十几分钟后清醒过来，发现自己光顾着看微博，不仅之前想说的东西已经忘了，自己的工作也耽搁了，不禁懊恼万分，决心好好工作；若干分钟后上面的场景再次上演。

因为最近在终端里呆的时间比较多，正好又在学习python，就希望试着用python写一个命令行里的发微博工具，只能写，不能看，工作吐槽两不误。

#准备工作

前期主要花时间看文档，找参考。

首先，新浪开放平台的[官方文档](http://open.weibo.com/wiki/%E9%A6%96%E9%A1%B5)是必要的参考资料。

另外，新浪推荐了来自[廖雪峰](http://weibo.com/liaoxuefeng)的pythonSDK，直接安装就能使用，不过因为追求独立性和扩展性，代码有些庞杂，反正我看不懂。

在别处找到了一个更轻量级的[脚本](http://lxyu.github.io/weibo/)，正如该项目所说的，使用了`requests`而不是`urllib`做相关的http操作，极大的简化了代码，提高了可读性。我主要以此为参开。


#思路

此次希望通过命令行的方式发送微博，这个实际上属于微博客户端的范畴，根据官方的文档，实现起来主要需要两个步骤：

- 身份认证，其实也就是登陆一下
- 调用API

下面详细的介绍。

#开工

##OAutho2.0用户身份鉴权

请求连接
https://api.weibo.com/oauth2/authorize?clientid=yourclientid&responsetype=code&redirect-uri=re
##调用api发微博

to do

- 先把request的文档看了吧
