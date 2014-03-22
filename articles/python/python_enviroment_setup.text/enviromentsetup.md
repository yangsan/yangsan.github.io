Title: ubuntu下python虚拟环境的配置问题
Date: 2014-1-15
Modified: 2014-1-15
Tags: python
Slug: python_enviroment_setup

昨天重做了一下系统，今天配置的时候有些新收获，写在这里。

#Enthought Canopy

*注：下面有更新，Enthought Canopy现在并非首选。*

自己用python首要是做计算的，昨天试着自己配置一下几个常用的包，弄了半天卡在依赖关系上了，没办法又用回[Enthought](https://www.enthought.com/)的Canopy。

Canopy是一个集成环境，把IDE和扩展包管理器等东西绑在了一起，做计算用的包也都有，装完所有东西都齐活了可以直接使用，非常舒服，后续的升级维护也很方便。

但有一个问题是Canopy会推荐你将其设置为系统默认的python环境，好处是你在终端里面也可以用Canopy下面的包，这样就免得你再次配置，非常方便。问题是，如果后面用python做其他方面的开发，直接就乱套了。

###Canopy的虚拟环境

Canopy采用了虚拟环境，所以并不影响系统本身的python环境，思路大致是把机器的环境复制一份出来放在其他路径下，运行维护就跟机器的环境再不相关了。

如果你也安装了Canopy，可以看一下，一般机器的python环境都在

    :::bash
	/usr/lib/python2.7

而Canopy默认情况下则把环境装在了

    :::bash
	/home/Enthought/Canopy

两者没有什么关联。

而之前提到的将Canopy设置为系统默认python环境，则是Canopy在`~/.bashrc`文件最后加了一句

    :::bash
	VIRTUAL_ENV_DISABLE_PROMPT=1 source ~/Enthought/Canopy_32bit/User/bin/activate

这样你每次开终端都是自动进入Canopy的虚拟环境下，这时如果做一些其他的工作，直接就和Canopy这套东西搅在一起了，后面会有匪夷所思的情况出现，之前因为不懂，我为此吐血三升。

所以建议如果还有别的要用到python的地方，就把这句话注释掉，或者在Canopy的配置菜单里，把`set as default`的选项勾选掉，二者干的其实是一件事情。

#Anaconda

[Anaconda](https://store.continuum.io/cshop/anaconda/)（本意是蟒蛇，搜索时可能会看到令人不适的图片，注意）是和Canopy类似的另一个python发行包，也是把一个python的虚拟环境连带各种包和包的依赖一并打包，下载后得到的是一个400m的bash文件，用bash运行一下根据提示走就可以无痛安装了，最后也是在`.bashrc`里面加了一句改了环境变量，不管怎么看都和Canopy如出一辙。

作为现在我眼中最佳的python环境，Anaconda主要有两个过人之处：

###1.原生支持Numba、Cython

其实本来我一直用Canopy的，但后来代码运行效率太低就想方设法的去做优化。python的慢是有目共睹的，其生态圈里面的优化方案也有不少，比较出名的大概是Cython了，后起之秀则有Numba等。

Numba最棒的地方在于其简单的用法和出色的效果，当然我连带Cython都试过后发现其实这些东西并没有外界宣称的那么美好后又回去学习c是另一个故事了。

不管怎么样Numba在我看来确实是一个很有前景的解决方案，尽管还不成熟，但使用得当的话还是一个不错的助力。

结果Canopy虽然可以安装Numba，但无法使用。又因为我希望[服务器]({filename}../linux/setuppythononcluster.md)上也有一个同样的环境，于是开始了一场艰苦卓绝的手动配置。

Numba由于其特殊的机制无法像一般的python扩展一样安装，有一部分依赖必须要自己编译，费时费力，还不一定能成功，我搞了两天把自己电脑的环境装好，但服务器因为系统太老，缺了几个必要的库而配置失败，当时真是十分难过。

最后抱着失败的想法试了试Anaconda，结果也就在下载安装包时花了点儿时间，安装完成之后一切都好了，就那么好了，不折腾，没痛苦，就那么好了。所以现在回头看看，真的不推荐自己手动去折腾，有那个时间不如去看看底层，看看算法。


###2.没有IDE

实际上提供了一个类似IDE的环境是Canopy的一大卖点，但用过之后就会意识到这只不过是把一个蹩脚的编辑器和一个ipython的窗口捏在一起攒出来的东西，一般IDE该有的debug、下断点功能统统没有，还非常不稳定，出点儿问题就卡死了。

python生态圈里其实并没有什么太出色的IDE，而像debug等的功能也都有三方包提供支持，一点都不缺。况且IDE用多了会让人变傻，没有其实更好。所以在这一点上我更喜欢Anoconda的做法。


#python的虚拟环境

我在重新配置pelican的时候注意到文档里面推荐把它装到一个虚拟环境里去。实际上推荐每一个新项目都放在独立的虚拟环境中，这样互不干扰最好。此外如果没有`root`权限，使用虚拟环境也是一个不错的[方案]({filename}../linux/setuppythononcluster.md)。

做法其实也很简单，主要用到[virtualenv](http://www.virtualenv.org/)这个包，可以装到系统的python环境中

    :::bash
	$ sudo pip install virtualenv

###基本用法

需要一个新的虚拟环境的时候就

    :::bash
	$ virtualenv ENV

这样实际上创建了`ENV/lib/pythonx.x/site-packages`，之后这个虚拟环境激活的状态下所有的包都是安装到这里的。

同时也创建了`ENV/bin/python`，这是一个python的解释器，如果你运行它，用的将是这个虚拟的环境。比如

    :::bash
	$ path/to/ENV/bin/pip

当然，为了方便，也可以

    :::bash
	$ source bin/activate

这这样做的结果实际上是修改了环境变量，从而将虚拟环境下的python解释器设为默认的，并且随后再安装其他的包也自动放到了虚拟环境下，后面就可以像正常的用法那样使用python了。

具体的实现方法可以参考`/bin/activate`脚本的内容。

如果想取消刚才的更改，可以

    :::bash
    $ deactivate

###额外的东西

参考`activate`脚本的内容，实际上可以自己手动更改环境变量得到同样的结果，但不建议这么做————除非你清楚自己在干什么。用`virtualenv`是一种省事且安全的做法。

但是，关于`activate`和`deactivate`等这些看似漂亮的设定也有[争议](https://gist.github.com/datagrok/2199506)，有兴趣的可以参考一下。
