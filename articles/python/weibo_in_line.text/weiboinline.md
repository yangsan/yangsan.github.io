Title: 一个用python写的命令行发微博工具开发记录
Date: 2014-1-27
Tags: python, 微博, project
Modified: 2014-2-14
Slug: weibo_in_line

#源起

一个常见的情景：工作中有感而发，想要吐槽两句，下意识的打开浏览器，进入新浪微博页面，然后就没有知觉了。十几分钟后清醒过来，发现自己光顾着看微博，不仅之前想说的东西已经忘了，自己的工作也耽搁了，不禁懊恼万分，决心好好工作；若干分钟后上面的场景再次上演。

因为最近在终端里呆的时间比较多，正好又在学习python，就希望试着用python写一个命令行里的发微博工具，只能写，不能看，工作吐槽两不误。

#准备工作

前期主要花时间看文档，找参考。

首先，新浪开放平台的[官方文档](http://open.weibo.com/wiki/%E9%A6%96%E9%A1%B5)是必要的参考资料。

另外，新浪推荐了来自[廖雪峰](http://weibo.com/liaoxuefeng)的[python SDK](http://michaelliao.github.io/sinaweibopy/)，直接安装就能使用，不过因为追求独立性和扩展性，代码有些庞杂，反正我没看懂。

在别处找到了一个更轻量级的[脚本](http://lxyu.github.io/weibo/)，正如该项目所说的，使用了`requests`而不是`urllib`做相关的`http`操作，极大的简化了代码，提高了可读性。我主要以此为参考。


#思路

此次希望通过命令行发送微博，这个实际上属于三方微博客户端的范畴，根据官方的文档，实现起来主要需要两个步骤：

- 身份认证
- 调用API

下面详细的介绍。

#开工

##OAuth2.0用户身份鉴权

如前面所说，这里要做一个第三方的微博客户端，所以在这个语境中主要有三个主体：1.用户，2.新浪微博，3.三方客户端。

一般我们登陆微博页面的行为只牵扯到1和2，这个简单明了。但如果想通过三方客户端来实现微博服务的话，则相当于在1和2之间夹了一个3，3势必要有更改用户内容的能力才行。可是又不能把账户密码提供给三方客户端，这时就要用到OAuth服务了。

OAuth服务的作用在于让三方客户端通过用户的授权在不知道用户账户密码的情况下访问用户微博数据。

这里以豆瓣读书的同步功能为例介绍OAuth的用户身份鉴权是如果运作的。

###豆瓣读书

豆瓣读书提供了与新浪微博同步的功能，也即在豆瓣标记了一本“已读”书目之后会自动发送一条微博到新浪。

要实现这个服务，首先需要用户来[设置](http://book.douban.com/settings/sync)账号关联。

用户通过点击设置页面的“关联”选项提出关联（也就是授权）请求，这时根据OAuth协议会跳转到新浪的一个授权页面，上面只有一个简单的登陆对话框，登陆之后该页面消失，而豆瓣的页面提示已经关联完成。注意到整个过程中，用户只是重新登陆了一下新浪微博，豆瓣是无法获知用户的账户密码的。

这整个过程背后的运作是这样的：

1. 在豆瓣的页面提出关联请求的时候，豆瓣组织了一个`https`请求，引导用户跳转至如下页面

        https://api.weibo.com/oauth2/authorize?client_id=YOUR_CLIENT_ID&response_type=code&redirect_uri=YOUR_REGISTERED_REDIRECT_URI
    
    这个格式是新浪要求的该页面也就是那个简易的登陆页面。实际使用时，这个连接里面`YOUR_CLIENT_ID`要替换成三方应用的`app_key`，`YOUR_REGISTERED_REDIRECT_URI`要替换成经过验证的回转目录，这点后面还有详细解释，豆瓣的话当然就跳转回豆瓣了。

2. 在上述页面登陆授权成功后则会跳转会刚才提到的回转地址：

        http://YOUR_REGISTERED_REDIRECT_URI?code=CODE

    注意到同时也获得了一个`CODE`，它是一串数字。

3. 上面的`CODE`被豆瓣获得以后就可以换取一个`Access Token`。方法是发送如下的`http`请求：

        https://api.weibo.com/oauth2/access_token?client_id=YOUR_CLIENT_ID&client_secret=YOUR_CLIENT_SECRET&grant_type=authorization_code&redirect_uri=YOUR_REGISTERED_REDIRECT_URI&code=CODE
   里面的相应内容要做替换，其他与前面一致的不再介绍，`YOUR_CLIENT_SECRET`要替换成三方应用的`app_secret`。

4. 所谓的`Acess Token`就在上一步`http`请求的返回值中包含着，这样只要这个`Token`不过期，豆瓣就能在不知道用户账户密码的情况下发送微博。具体的操作请求格式可以参考新浪官方的[API](http://open.weibo.com/wiki/%E5%BE%AE%E5%8D%9AAPI)。

其他的网站或者三方客户端通过授权发送微博的过程都和这个差不多。下面介绍一下如何做一个自己的客户端。

###自己的三方客户端

首先需要在[微博开放平台](http://open.weibo.com/)上根据自己的需要创建一个`app`，我选了桌面客户端应用。接着按要求填写应用的名称、应用地址等信息。

创建成功后要求你对刚才填写的应用地址进行验证，而这个地址也就是前面提到的回转地址。

验证成功后你会得到一个`app_key`，一个`app_secret`，这时你的身份虽然还没有通过审核，但已经可以使用这个应用进行相关操作了。
