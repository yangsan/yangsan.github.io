Title: 去掉pelican页面代码块中的红框
Date: 2014-1-22
Modified: 2014-1-22
Tags: pelican, tircks
Slug: avoid_red_block

刚开始用pelican生成博客页面的时候发现代码块中经常有一些红框，很碍眼，就像这样

    Title: 去掉pelican页面代码中的红框
    Date: 2014-1-22


后来看到同样用pelican的[Frantic](http://frantic1048.com/qu-diao-pelicansheng-cheng-de-dai-ma-qu-kuai-zhong-de-hong-kuang.html)指出，这个红框框主要时为了显示出代码高亮时遇到的错误，是由主题里面`pygment.css`这个文件规定的。进去查看可以找到这样一句

    :::css
    .highligt .err {border: lpx solid #FF0000} /Error/

如果不喜欢这个效果，可以把这句注释掉。

不过其实也没有必要这样，正如前面所提到的，出现红框框是因为处理代码高亮时遇到了错误，所以你把它弄对就好了。

pelican的代码高亮用的是`pygment`的系统，平时能见到的语言种类都是支持的，不过需要你在代码块前面声明一下，也即加上`:::语言名`，这样就能正常的高亮了。

比如前面带
