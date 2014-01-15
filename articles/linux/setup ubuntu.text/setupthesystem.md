Title: ubuntu的简单配置
Date: 2014-1-15
Modified: 2014-1-15
Tags: linux
Slug: setup ubuntu

接触ubuntu不过两个月，之前装了一个试玩，熟练之后越发的喜欢了，准备把工作环境迁移过来。不过之前只给了ubuntu 30g的磁盘空间，明显不够用，而且gnome3各种反人类，昨天心血来潮把原来的系统删了，扩了一下磁盘，重装。今天重新配置，顺便写在这里，后面方便查阅。

#装系统

###分区

我搞的是双系统，windows虽然讨厌，但还真离不开。没装ubuntu之前，硬盘分了两个区，c盘是主分区，用来装系统，剩下的扩展分区是一个d盘，存数据。

要再装个ubuntu的话就得再分一个区出来，做法很简单，（如果有的话）在windows下面，找到磁盘管理器，在原来的d盘上右键，压缩磁盘，跳出来的对话框可以调整大小，我弄了130g出来，点确定，过一会儿就能看到多了一个130g的空闲分区，windows下面的操作就到此为止了。

注意虽然压缩磁盘对原有的数据没影响，但磁盘操作都有丢数据的风险，最好提前做好备份。



###做u盘安装盘

先去找一个顺眼的ubuntu的iso镜像，官网上就能下，这次我选了xubuntu，就是ubuntu换了张皮，但很多细节做的很舒服。

而后google一下UNetbootin，是个跨平台的ubuntu安装盘制作工具。找个空u盘，插电脑上，4g的就够了。进UNetbootin，把刚才的iso镜像选上，点ok过一会儿就制作好了。

如果没有别的事儿就可以重启了，记得狂按F12，选从USB设备启动，后面跟着提示走。

关于ubuntu的文件系统和分区问题，不懂建议还是先找本书看看，并不复杂。

#配置搜狗输入法

ubuntu似乎内置了iBus输入法，把语言支持弄好就可以用了，但也只是能用而已。

好消息是，最近有高手把搜狗输入法移植了过来，不用考虑了，装吧。


首先把iBus卸载了

	$ sudo apt-get remove ibus

接着在`sources.list`添加源

	deb http://ppa.launchpad.net/fcitx-team/nightly/ubuntu quantal main 
	deb-src http://ppa.launchpad.net/fcitx-team/nightly/ubuntu quantal main 

在终端中，输入

	sudo apt-get update
	sudo apt-get install fcitx fcitx-config-gtk fcitx-sunpinyin fcitx-googlepinyin fcitx-module-cloudpinyin  fcitx-sogoupinyin

后一句的意思是安装fcitx输入法，安装fcitx输入法配置工具，安装搜狗输入法。

装好以后在系统语言设置里面把fcitx设置为默认输入法，在fcitx的配置工具里把搜狗输入法挪到第二位。注销一下，大功告成。

这篇博客就是用搜狗写的，很舒服，不枉一番折腾。


