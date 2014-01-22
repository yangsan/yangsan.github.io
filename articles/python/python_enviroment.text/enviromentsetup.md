Title: ubuntu下python虚拟环境的配置问题
Date: 2014-1-15
Modified: 2014-1-15
Tags: python
Slug: python_enviroment

昨天重做了一下系统，今天配置的时候有些新收获，写在这里。

#Enthought Canopy

自己用python首要是做计算的，昨天试着自己配置一下几个常用的包，弄了半天卡在依赖关系上了，没办法又用回[Enthought](https://www.enthought.com/)的Canopy。

Canopy是一个集成环境，把IDE和扩展包管理器等东西绑在了一起，做计算用的包也都有，装完所有东西都齐活了可以直接使用，非常舒服，后续的升级维护也很方便。

但有一个问题是Canopy会推荐你将其设置为系统默认的python环境，好处是你在终端里面也可以用Canopy下面的包，这样就免得你再次配置，非常方便。问题是，如果后面用python做其他方面的开发，直接就乱套了。

###Canopy的虚拟环境

Canopy采用了虚拟环境，所以并不影响系统本身的python环境，思路大致是把机器的环境复制一份出来放在其他路径下，运行维护就跟机器的环境再不相关了。

如果你也安装了Canopy，可以看一下，一般机器的python环境都在

    :::bash
	/usr/lib/python2.7

下面，Canopy默认情况下则把环境装在了

    :::bash
	/home/Enthought/Canopy

下，两者没有什么关联。

而之前提到的将Canopy设置为系统默认python环境，则是Canopy在`~/.bashrc`文件最后加了一句

    :::bash
	VIRTUAL_ENV_DISABLE_PROMPT=1 source ~/Enthought/Canopy_32bit/User/bin/activate

这样你每次开终端都是自动进入Canopy的虚拟环境下，这时如果做一些其他的工作，直接就和Canopy这套东西搅在一起了，后面会有匪夷所思的情况出现，之前因为不懂，我为此吐血三升。

所以建议如果还有别的要用到python的地方，就把这句话注释掉，或者在Canopy的配置菜单里，把`set as default`的选项勾选掉，二者干的其实是一件事情。

#python的虚拟环境

我在重新配置pelican的时候注意到文档里面推荐把它装到一个虚拟环境里去。实际上推荐每一个新项目都放在独立的虚拟环境中，这样互不干扰最好。

做法其实也很简单，主要用到[virtualenv](http://www.virtualenv.org/)这个包，可以装到系统的python环境中

    :::bash
	$sudo pip install virtualenv

###基本用法

需要一个新的虚拟环境的时候就

    :::bash
	$virtualenv ENV

这样实际上创建了`ENV/lib/pythonx.x/site-packages`，之后这个虚拟环境激活的状态下所有的包都是安装到这里的。

同时也创建了`ENV/bin/python`，这是一个python的解释器，如果你运行它，用的将是这个虚拟的环境。比如

    :::bash
	$path/to/ENV/bin/pip

当然，为了方便，也可以

    :::bash
	$source bin/activate

这这样做的结果就是把你的`$PATH`指到了当前的虚拟环境里，后面可以像正常的用法那样使用python了。结束的时候用命令`deactivate`就又把路径指回去了。
