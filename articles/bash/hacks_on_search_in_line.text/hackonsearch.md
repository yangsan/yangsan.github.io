Title: 通过在bash配置文件里定义函数实现在命令行快速搜索互联网
Date: 2014-2-13
Tags: hacks, bash
Slug: hacks_on_search_in_line

#源起

最近在学写shell脚本搞自动化，遵照“发现时常重复的工作流程，将其自动化”的精神，我注意到一个常有的情景是：工作、浏览网页时需要百度/google一个关键词，或者在豆瓣/新浪爱问上搜索一本书。

本身用加了插件的浏览器这些事情不算麻烦，不过多接触浏览器一秒钟就多一分被干扰（常在电脑前工作的同志应该懂的）的机会，所以能在命令行解决最好，也权当练习了。


#思路

解决思路也很简单，在命令行里输入

    :::bash
    webbrowser http://example.com

就可以新打开一个浏览器标签，并打开相应的网页，一般的网页直接将网址填上去就好。

但这里考虑的是查询的问题，所以网址后面还要加上query的部分，这块不需要太多的知识，下面举例说明。

###栗子

以豆瓣读书为例，直接在浏览器中登陆其页面：[book.douban.com](http://book.douban.com)，在搜索框里随便填一个关键词，比如“追风筝的人”，点搜索，注意跳转后的结果页面的地址变成了

    http://book.douban.com/subject_search?search_text=追风筝的人&cat=1001

问号后面的就是http请求中query的部分，而且也很好理解，`search_text=`后面的就是刚填的关键词了，这时我们将“追风筝的人”几个字换成“尘埃落定”，再按下回车，跳转到的页面正是搜索“尘埃落定”的结果。

所以下面要做的，就是在本地把整个http请求组织好，然后送到浏览器里去。

#脚本部分

暂时以函数的形式写在`.bashrc`文件里。

还是以豆瓣为例：

    :::bash
    #定义db函数
    db() {
        if [ $1 ] #检查是否有参数（也就是关键词）传递进来
        then 
            query="$1"
            for arg in ${*:2}
            do
                query+="+"
                query+="$arg"
            done #这一部分主要将传递进来的若干个关键词用+串联起来，这是query的格式
            #下面就可以启动浏览器了，我用的是chromium
            chromium-browser "book.douban.com/subject_search?search_text=$query&cat=1001"
        else #没有关键词的情况下就打开豆瓣主页
            chromium-browser "www.douban.com"
        fi
    }
实现的结果就是在命令行中输入

    :::bash
    db 书名 

就自动打开相应的豆瓣读书的搜索页面。

类似的还可以对baidu、google和新浪爱问做相同的处理。不过新浪爱问对中文处理有问题，结果是乱码，这个可能涉及到编码问题，暂时没法处理，其他的都测试有效，运转良好。
