Title: 给pip换国内的pypi镜像
Date: 2014-2-27
Tags: python
Slug: pip_pypi_mirror

今天发现[pypi.python.org](https://pypi.python.org/pypi)挂了，用`pip`搜索安装都不正常，索性换个国内的镜像试试。

##镜像地址

根据[这个](http://www.pypi-mirrors.org/)网站的说法，目前国内的镜像主要有

- [豆瓣](http://pypi.douban.com/)
- [华中科技大](http://pypi.hustunique.com/) 

推荐电信/联通用户用前者，教育网用户用后者。

##用法

####单次下载可以指定镜像

    :::bash
    $pip install -i http://pypi.hustunique.com/simple <package name>

####全局设定

在`~/.pip/pip.conf`配置文件中加上

    [global]
    index-url = http://pypi.hustunique.com/simple

地址最后的simple一定不能忘了。

改过之后速度有所提升，重要的是稳定多了。
