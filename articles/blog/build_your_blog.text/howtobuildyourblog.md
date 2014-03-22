Title: 利用Pelican和Github简单迅速地搭建自己的博客
Date: 2013-12-14
Modified: 2013-12-15
Tags: how to, pelican, github, git, markdown, blog
Slug: build_your_blog


#思路

一般而言，搭建一个独立博客需要三样东西：

- 博客软件
- 托管服务器
- 独立的域名

博客软件用来生成你的博客页面，而生成好的页面则需要放到托管的服务器上才能被别人看到。

##博客软件

这里我选择`Pleican`作为博客软件主要是出于以下的考虑：

- 基于`Python`，简单易上手
- 生成静态页面，不需要数据库，访问速度快，易于维护
- 支持`markdown`标记语言

和著名的`wordpress`相比，`pelican`搭建起来的博客功能和界面会相对单薄一些，但对于我来说绝对够用，况且写博客的初衷也是想把注意力放在内容上，所以工具越简单反而越好。


##托管服务器

这里选择`github`的原因也很简单：

- 免费
- 省事
- 可以直接用`git`进行版本管理

##磨刀

这份教程中提到的方法虽然简单，但仍旧需要你有一些基础，主要有

####1.`python`基础

你至少应该能够配置`python`的环境，能够顺利的安装`python`的插件,了解一些简单的`python`语法。

####2.了解`Markdown`

如果你没听说过也不要紧，[这里](http://joinwee.com/lesson/10/)有一份简短的介绍，详细的语法说明则可以在[这里](http://wowubuntu.com/markdown/)找到。

####3.对`git`/`github`有所了解

如果不了解也同样没有关系，[这个](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)一站式的教程可以帮到你。

这里多说一点。`git`本身就是追踪文本的系统，虽然设定上是给程序做版本管理的，但未尝不能拿来管理文章。甚至，我相信`git`+`markdown`等轻量级标记语言代表了一种写作的未来。

---

#动手

这里提一下，我的配置环境是ubuntu13.04。windows则需要多一些折腾，请自行google。

##安装`Pelican`

这里假设你已经安装好了python2.x的环境，并装好了`pip`。可以直接用下面的命令安装`pelican`

    :::bash
	pip install pelican

要使用`markdown`，还需要一个`markdown`的支持

    :::bash
	pip install markdown

##首次配置

选定一个目录存放你的博客文件，我的叫`blog`

    :::bash
	mkdir blog
	cd blog

在你的`blog`目录下使用命令

    :::bash
	pelican-quickstart

接下来会询问你一系列问题，除了名字等必须要填的，其他建议使用默认值，随后还可以通过配置`pelicanconf.py`文件进行修改。

运行结束之后会生成的目录结构

    :::bash
	blog/
	├── content
	│   └── (pages)
	├── output
	├── develop_server.sh
	├── Makefile
	├── pelicanconf.py     
	└── publishconf.py

`pelican`的逻辑是，你把写好的用markdown写好的纯文本文档放进`content`这个目录下面，运行程序，就可以将你的整个博客页面生成到`output`中去了。

至此，文件系统部分已经配置完成了，接下来可以试着写一篇文档了

##第一篇博文

创建一个叫`first.md`的文档放到`content`目录下。`Pelican`对内容的格式有些要求，每一篇博文的开头都应该是这样的：

    :::markdown
	Title: 第一篇博客
	Date: 2013-12-12 10:16
	Category: 测试
	Tags: 心情
	Author: 煎挠橙 

	这里开始可以书写正文了
	blabla....

所以你应该看明白了，必须要通过每篇博文开头的部分来告诉`pelican`这篇文章的标题、标签、分类等信息，以便最终呈现在博客页面上。其中，标题、时间是必填的，如果缺失则会被`pelican`忽略掉。

##生成页面

写完你的第一篇博文后，你应该还在`content`目录下，先回到blog根目录下

    :::bash
	cd ..

使用下面的命令`pelican`系统会将你放在`content`目录下的`.md`文档转换成网页文件

    :::bash
	make html

当然，你也可以让`pelican`在每次检测到文件变化的时候都重新生成一次网页，使用下面的命令

    :::bash
	make regenerate

实际上此时如果你去`output`目录下就会看到生成的页面已经躺在那里了，但为了方便本地的调试，你可以用下面的命令开启一个本地的服务器

    :::bash
	make serve

这样，通过访问 http://127.0.0.1:8000 你就已经可以看到你的博客页面了！

不过这样光是调试就得开两个终端，稍麻烦，`pelican`实际上还提供了一个命令，可以代替上面两个

    :::bash
    make devserver

我这儿一开始用这个命令一直报错，注意到这个命令要调用路径下的`develop_server.sh`这个脚本，但这个脚本本身没有执行权限，一般而言用下面的命令改一下它的权限就行

    :::bash
    chmod u+x develop_server.sh

可是我这儿不知为何根本改不了，没办法就在`Makefile`上下功夫。打开`Makefile`找到下面几句

    :::make
    devserver:
    ifdef PORT
        $(BASEDIR)/develop_server.sh restart $(PORT)
    else
        $(BASEDIR)/develop_server.sh restart
    endif

改成下面的样子就可以了

    :::make
    devserver:
    ifdef PORT
        bash $(BASEDIR)/develop_server.sh restart $(PORT)
    else
        bash $(BASEDIR)/develop_server.sh restart
    endif

调试完毕后你应该关闭之前开启的服务器

    :::bash
	./develop_server.sh stop

至此，尽管有些丑，但你已经完成了自己博客的创建，随后只需要把你的文章放进`content`目录下面并运行以上命令就可以轻松生成博客了。如果需要备份，也只需要将`content`目录下的文件备份好就行，非常省心。

当然此时别人还无法访问到你的页面，下面就教你如何把你的博客放到网上去。

##在`github`上创建个人页面

首先你要有个`github`账号。`github`为每个账号提供一个子域名以供存放个人页面，使用的方法是创建一个新的版本库，命名为

    :::bash
	username.github.io

其中`username`必须为你的`github`用户名，否则将无法启动页面。

这时你新创建的版本库是空的，你会获得一个地址

    :::bash
	https://github.com/username/username.github.io.git

先记下来。

接下来要做的是将你本地`output`目录下的所有内容都推送到你刚刚建立的远程版本库中。在本地，进到`output`目录下面

    :::bash
	cd output

在这里新建一个本地的版本库

    :::bash
	git init

建议按照`github`的惯例添加一个新的`readme`文档

    :::bash
	touch README.md

接下来可以将目录下面所有的文件都添加进缓存区

    :::bash
	git add .

可以提交了

    :::bash
	git commit -m 'First commit'

下面将你的本地仓库和远程仓库关联起来，还记的之前获得的一个地址吧，用在这里

    :::bash
	git remote add origin https://github.com/username/username.github.io.git

最后将本地仓库推送到`github`上就好，由于是第一次，用上`-u`字段

    :::bash
	git push -u origin master

待推送成功，稍等片刻，登陆

	username.github.io

应该就能看到你的博客了，没错，它已经正式上线了。此后每次变更博客内容或设置，只需事后将`output`目录下的内容推送到`github`就可以了，听上去和做上去都很简单。

这样，如果你不在意域名的问题，你的博客就算搭建成功了，而且没有花费一分钱！但是基本主题这么丑，博客的各种功能也都还没有这怎么行呢，接着看。

---

#锦上添花

##使用主题

`pelican`目前提供了数款主题可供选择。先到`pelican`这个项目的`github`页面上把主题的版本库`clone`到本地，在你选定的本地目录下面

    :::bash
	git clone https://github.com/getpelican/pelican-themes.git

进`pelican-themes`目录

    :::bash
	cd pelican-themes

你可以浏览该目录下的文件，每个子目录存放了一个主题，里面会留有一个截图供你预览。比如我看中了`bootstrap`这一款，使用命令

    :::bash
	pelican-themes -i bootstrap

安装该主题。这时使用命令

    :::bash
	pelican-themes -l

查看已安装主题，应该就能看到刚刚安装的`bootstrap`主题了。这还不够，你还需要在`pelicanconf.py`文档中加上一句

    :::python
	THEME = 'bootstrap'

这样你在生成博客页面时新主题就被应用上了。

##添加静态的页面

`pelican`提供了一种不同于一般博客文章的'页面'方便你存放一些需要置顶的信息，比如`about`页面和`contact`页面。

创建方法也很简单，只需要在`content`目录下再新建一个名为`pages`的目录，将你想要展示的内容放进去就行，格式和普通的博客文章相同。

##安装评论系统

由于`pelican`生成的是静态页面，没有数据库的支持所以本身无法实现评论的功能，不过我们还有第三方的服务可以选择。

这里推荐`disqus`，功能齐备，免费。

到[官方页面](www.disqus.com)上注册一个账号。找到'Add Disqus to your site'选项，在跳转后的页面上填写的网站名字，你填写的名字也将是你的'shortname'，记好了。

接下来在你的`pelicanconf.py`文档中添加以下字段

    :::python
	DISQUS_SITENAME = shortname

用你自己的'shortname'替换进去，剩下的交给`pelican`。

#自动化

熟练之后会发觉整个工作流程实际上只有一下几步：

1. 进入装了`pelican`的python虚拟环境
2. 打开`pelican`的本地调试功能
3. 打开博客的本地页面
4. `cd`到存放博客的content目录下面
5. 用`vim`编辑文章，调试
6. 使用`git`将生成的网站推送到服务器上

虽然不多，但是用过一段时间后还是觉得繁琐，恰巧我是在ubuntu下面维护这个博客的，不搞搞自动化怎么过瘾，折腾了一下差不多可以了，下面是步骤。

##期望

最终的实现效果大致是这样的：在终端中输入“blog”字段，自动完成上面提到的1-4步，编辑文章自然要手动，这个没的说，最后的推送部分最好也手动完成，commits还是要认真写的。

##实现

###1.

一开始先写了一个bash的脚本完成1,2两步，为什么要这样随后说，内容如下

    :::bash
    #!/bin/bash

    . ~/virtualenvs/pelican/bin/activate #打开虚拟环境
    cd /media/kevin/MainDisk/1/blog/ #cd到我的博客目录下面
    bash develop_server.sh restart #打开pelican的自动监测-更新-本地端口功能
    read answer #防止新打开的窗口被关闭

第一行主要是把python虚拟环境的配置脚本source一下，关于虚拟环境更多的信息可以参考[这一篇]({filename}../python/enviromentsetup.md)。

第二行则是进入到我的blog目录下面，因为相应的Makefile和bash脚本都在这里放着。

第三行的作用则和前面提到的`make devserver`一样，实际上如果你去查看Makefile的话，会发现输入`make devserver`执行的就是这一条命令，我不想绕弯就直接写成这样了。

第四行则作用则有点像你在windows下面编译c程序，因为跳出来的窗口在程序运行完之后就自动关闭，便往往在最后留一个等待输入的命令让程序先等着。

接着将该脚本保存为`runpelicanserver.sh`，更改其权限以便能够直接运行

    :::bash
    $ chmod u+x runpelicanserver.sh

并将其放在PATH环境变量中包括的路径下面，这样随时随地都可以调用。

###2.

剩下的工作我选择在`.bashrc`文件中编写function来完成，添加以下字段：

    :::bash
    blog(){
        xfce4-terminal -e runpelicanserver.sh &
        xfce4-terminal --working-directory=/media/kevin/MainDisk/1/blog/content/
        google-chrome 127.0.0.1:8000 &
    }

因为我使用的是xubuntu发行版，默认的终端是xfce4-terminal，你在使用这套东西之前应该先确认一下自己的系统配置。

这个func叫blog，每次我在终端中键入`blog`字段时，大括号里面的命令会被依次执行。

第一行表示重新打开一个终端窗口，`-e`字段表示执行后面的命令，也即`runpelicanserver.sh`脚本，最后的`&`表示后台运行，因为新打开 的窗口是原来窗口的子进程，如果不后台运行的话关闭原来的窗口也会影响到新窗口的行为。要注意`runpelicanserver.sh`一定要放在环境变量下，不然就要加上完整的路径名。

第二行则表示重新打开一个终端窗口，其工作路径为我的博客路径，方便稍后编辑文章。

最后一行则表示使用`chrome`打开本地调试页面。

整个工作完成之后就可以达到预期的目的了。不过我遇到的一个问题是在运行`runpelicanserver.sh`时常常无法顺利打开`pelican`的本地调试端口，导致本地的页面打不开，原因不明，所以不得不重新跑一下脚本，这也是为什么把它独立出来的缘故。
