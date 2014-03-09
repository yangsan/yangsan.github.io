Title: Linux安装gsl库
Tags: clang
Date: 2014-3-9
Slug: howto_install_gsl

用python做计算，虽然优化花了很大功夫，但速度仍旧不够理想，所以我又开始学习c语言了。c本身并不以计算能力见长，但运行效率没问题，相关的库也很完善，平常能用到的都有涉及，这里记录一下gsl（GNU scientific lib）的安装和配置过程。

#下载安装

因为是开源库，直接在[官网](http://www.gnu.org/software/gsl/)下载就好，只有几兆，体积不大。

我选了最新的版本，得到了一个压缩包`gls-1.16.tar.gz`

老规矩，先解压，再进解压目录

    :::bash
    $ tar zxvf gsl-1.16.tar.gz
    $ cd gsl-1.16

接下来是安装的常规流程，值得注意的是默认安装目录为`/usr/local/include`和`/usr/local/lib`，这个先记住，随后有用。我在服务器上装了一遍，把库装在了本地，所以命令如下

    :::bash
    $ ./configure --prefix=/home/kevin/.local
    $ make
    $ sudo make install

如果没什么差错的话在安装目录的`/include`下面就可以看到`gsl`的库了。

#编译，链接和环境变量

上面装完还没有万事大吉，c的库使用起来完全没有python那么便利，还需要一些设置。

###编译

官方文档给力一个调用gsl贝塞尔函数的例子

    :::c
    #include <stdio.h>
    #include <gsl/gsl_sf_bessel.h>

    int
    main (void)
    {
        double x = 5.0;
        double y = gsl_sf_bessel_J0 (x);
        printf ("J0(%g) = %.18e\n", x, y);
        return 0;
    }

调用方法就是在开头处加上`#include <gsl/gsl_sf_bessel.h>`，如果默认情况下编译不通过，则需要加上一个路径的flag，如果你把库装在了别的地方，则需要进行修改

    :::bash
    $ gcc -Wall -I/usr/local/include -c example.c -o example.o

###链接库
紧接着则要进行库连接（我真希望我知道自己在说什么，但确实不知道）

    :::bash
    $ gcc -L/usr/local/lib example.o -lgsl -lgslcblas -o example

这样就生成了一个可执行文件，但这样做显然不够geek，下面介绍用make工具来进行自动化操作。

###Makefile

在放`.c`源文件的目录下面新建一个`Makefile`，内容如下：

    :::make
    CFLAGS=-Wall -g -DNDEBUG
    CC=gcc
    MACPORTS = /usr/local
    CPPFLAGS = -I$(MACPORTS)/include
    LDFLAGS = -L$(MACPORTS)/lib
    LDLIBS = -lgsl -lgslcblas

    %:%.o
        $(CC) $(LDFLAGS) $< -o $@  $(LOADLIBS) $(LDLIBS) 

    %.o:%.c
        $(CC) $(CFLAGS) $(CPPFLAGS) -c $< -o $@

**注意:**写`Makefile`的时候一定要把里面的两个tab换成真tab，我这网页上的很可能是四个空格，会导致make出现bug。当然，请顺便也把路径改了。

妥当以后在源文件目录下可以直接

    :::bash
    make example

生成可执行文件，并且example可以换成你的文件名。

到这儿还没完，不出意外你执行刚才的`./example`时会报错，还需要修改一下环境变量来使程序正常运行。

###环境变量

很简单

    :::bash
    $ LD_LIBRARY_PATH=/usr/local/lib
    $ export LD_LIBRARY_PATH
    $ ./example

这样运行就无误了，正确结果应该是

    :::bash
    $ J0(5) = -1.775967713143382920e-01

当然为了方便，我把环境变量写进`.bashrc`里了。
