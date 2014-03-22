Title: 在ubuntu上编译中文latex文档
Date: 2014-3-13
Tags: latex, linux
Slug: chinese_latex_ubuntu

tex大名不再多说，作为需要编译的文档，外加原生不支持中文这样的设定，给人感觉难用是正常的。今天咬咬牙看了些资料，发现在前人的努力下编译中文已经是很简单的事情了，虽然原理依旧复杂，需要注意的地方依旧很多。

具体的解决方案为XeLaTeX + xeCJK + ctex。

在windows平台下还是建议使用ctex套件，该套件把引擎编辑器等等东西一起打了包，一次安装无痛使用。

linux下面则因为字体配置方案不统一的问题需要一些配置，下面是具体方法。

#texlive

后端推荐使用texlive2012，可以找一个光盘镜像挂载起来，在挂载目录下直接

    :::bash
    sudo ./install-tl

注意安装完成以后根据提示往`.bashrc`里面添加一下环境变量

    :::bash
    export PATH=/usr/local/texlive/2012/bin/x86_64-linux:$PATH
    export MANPATH=/usr/local/texlive/2012/texmf/doc/man:$MANPATH
    export INFOPATH=/usr/local/texlive/2012/texmf/doc/info:$INFOPATH

这样texlive就算安装配置好了，CJK、ctex宏包和XeTeX引擎都内置在其中，稍后会用到。

#安装中文字体

因为版权问题texlive没有携带诸如宋体、黑体等的win字体，不过我们可以直接去windows系统的`windows/fonts/`目录下面拷贝`sim*.ttf`（宋体是simsum.ttc）出来。

拷出来的字体放在`/usr/share/fonts/windows`下面，更改一下权限

    :::bash
    sudo chmod 644 /usr/share/fonts/windows/*

接着刷新字体缓存

    :::bash
    sudo fc-cache -fv

字体安装成功后可以通过一下命令查看

    :::bash
    fc-list :lang=zh

上面显示出的结果才是系统所识别的字体名字，先记住。

###配置中文字体

因为众多linux发行版中的字体名称不不统一所以texlive默认的配置往往无法正常工作，这就需要手动去更改配置。

    :::bash
    sudo vim /usr/local/texlive/2012/texmf-dist/latex/ctex/fontset/ctex-xecjk-winfonts.def

根据刚刚查看到的系统安装的字体情况，将以上配置文件中不合适的部分如`ItalicFont={[simkai.ttf]}`改为`={KaiTi}`。这样字体就配置完成了。

#测试

    :::bash
    touch test.tex

测试文件的内容为

    :::tex
    \documentclass[UTF8]{ctexart}
    \begin{document}
    测试中文
    \end{document}

不出意外的话`pdflatex`引擎能够成功编译上面的文档。

其中的`ctexart`是LaTeX中article文档类的中文本地化版本，如果你想使用其他的文档类，也可以这样

    :::tex
    \documentclass[UTF8]{article}
    \usepackage{ctex}
    \begin{document}
    测试中文
    \end{document}

二者效果一致，在编译时ctex在底层调用CJK，并添加了很多本地化的成分，比如“Figure 1”会变成“图 1”等，方便不少。

调用字体则可以用`\heiti`、`\kaishu`这些。

#另一个选项：XeTeX

上面的方案虽然解决了中文的编译问题，但限制是只支持win下面经典的六套字体，具体原因则牵扯了版权技术等等的问题，我也没搞太明白。

这样的情况下可以任意调用系统字体的XeTeX引擎横空出世，并且包含在各大tex的发行版中，装了texlive的就可以直接调用。

值得注意的一点是，XeTeX基于Unicode，要求它的源文件编码必须是utf8（业界良心），win用户在保存文件的时候注意一下编码。

下面测试一下：

    :::tex
    \documentclass{article}
    \usepackage{xeCJK}
    \setCJKmainfont{微软雅黑} %我按照前面的方法装了微软雅黑字体，没有可以选文泉驿正黑，或是任何系统字体
    \begin{document}
    测试中文！
    \end{document}

编译时用`xelatex`，调用xeCJK宏包，直接生成pdf。

其实还可以这样

    :::tex
    \documentclass{ctexart}
    \setCJKmainfont{微软雅黑}
    \begin{document}
    测试中文
    \end{document}

依旧使用`xelatex`编译，这样做的虽然看似用了CJK的底层，但实际上XeTeX引擎下ctex调用的还是xeCJK的宏包。

这样写的好处是如果没有指定字体或者指定的字体不存在，将使用宋体黑体等，不影响编译，兼容性好。
