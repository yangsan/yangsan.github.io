Title: 无root权限的情况下在集群上配置个人python环境
Date: 2014-2-27
Tags: python, linux
Slug: set_up_python_on_clusters

昨天刚问管理员师兄要了个服务器的账户，准备折腾一番。因为所里面用`python`的人不多，环境基本没配置，仅有的标准`python`库也很老了。

而且因为服务器上惊人的预装了`SuSE Linux`（What？），连源都没有，我磨了半天嘴皮子管理员师兄还是拒绝给上面的`python`升级，还说让我随便改一下环境变量就好了。

于是我折腾了4个小时，很“随意”的搞定了。这里做一个备份，省的回头忘了。

#解决思路

1. 在我的`/home`下先装一个`python 2.7.6`版本
2. 用`virtualenv`隔离一个python环境出来
3. 修改一下`bash`的配置文件，每次登陆时都把默认的`python`环境指向本地

这样处理过后

1. 我得到了一个想要的`python`版本
2. 不需要`root`权限
3. 使用了虚拟环境，不怕折腾
4. 和一般的`python`环境用法无异

基本就满足了日常的使用需求。

#具体步骤

###在用户home下安装python

先去官网的[下载页面](http://www.python.org/download/releases/2.7.6/)找一个源代码的包，下载下来。
比如得到了`Python-2.7.6.tgz`这个包。
    
    :::bash
    $tar zxvf Python-2.7.6.tgz
    $cd Python-2.7.6
    $./configure --prefix=/home/kevin/.local
    $make
    $make install

这样就在本地安装好了一个`python`环境，运行的时候可以

    :::bash
    $/home/kevin/.local/bin/python

并且与系统默认的`python`环境没有冲突。

当然这样很不方便，所以还有一些工作要做。

###创建一个python的虚拟环境

这里要用到`virtualenv`这个包，更多的信息可以参考[这里]({filename}../python/enviromentsetup.md)。

去[pypi](https://pypi.python.org)上找一个`virtualenv`的源码包，下载下来。
比如得到了`virtualenv-1.11.4.tar.gz`这个包。

    :::bash
    $tar zxvf virtualenv-1.11.4.tar.gz
    $cd virtualenv-1.11.4

主要用到里面`virtualenv.py`这个文件。

    :::bash
    $/home/kevin/.local/bin/python virtualenv.py MY_VIRTUAL_ENV

这样，将可以在你指定的目录下得到一个复制于刚刚安装在`/home`下的`python`环境。

以上的做法实际上是在不安装`virtualenv`的情况下使用它，之所以要这样，是为了避开系统默认python环境的干扰。

当然，如果刚才的命令改为

    :::bash
    $python virtualenv.py MY_VIRTUAL_ENV

得到的就是系统默认`python`环境的拷贝。

###将虚拟环境设置为系统的默认python环境

若想使用虚拟环境，首先要将其激活，做法是将`MY_VIRTUAL_ENV/BIN/`目录下的`activate`脚本`source`一下

    :::bash
    $source MY_VIRTUAL_ENV/bib/activate

为了省事，可以将上面的命令写进`.bashrc`文件，这样每次进入bash shell就自动激活了这个虚拟环境，随后就可以平时一样使用python的解释器了。

ps：如果想去掉激活了虚拟环境后终端标题前面的提示信息，需要将变量`VIRTUAL_ENV_DISABLE_PROMPT`的值设定为非零，具体可以这样写

    VIRTUAL_ENV_DISABLE_PROMPT=1 source MY_VIRTUAL_ENV/bib/activate

这样就没有任何异常了。

#不折腾的方法

其实最后的生产环境中我并没有采用上面的方案，因为最后无论如何都没法成功在服务器上装好Numba，抱着试一试的想法用了Anaconda，结果就这么好了，具体情况可以参考[这篇]({filename}../python/enviromentsetup.md)。
