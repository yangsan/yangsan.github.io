Title: 用Matplotlib做一个交互式的德劳尼三角生成器
Date: 2013-12-30
Tags: python, matplotlib, 可视化
Slug: aDelaunaytrigen
Modified: 2014-4-25

正在看的一篇文献里用到了德劳尼三角形的算法来界定包含了一些点的空间，搜索了一下，[这里](http://www.cs.cornell.edu/Info/People/chew/Delaunay.html)有一个在线的小程序，让你可以通过鼠标在演示界面上点击的形式确定点的位置并动态的生成德劳尼三角形。

唯一的问题在于这个小程序是`java`写的，而我不懂`java`，遂用`python`自己写一个，也方便h后面给同事们展示。

##Matplotlib的Event

其实这样的交互式程序应该用`GUI`包来完成，为此我甚至把`Tkinter`的文档都翻了一遍，偶然的机会我注意到常被用来画图的`matplotlib`居然也可以实现交互功能，简直喜出望外。

官方的文档请参考[这里](http://matplotlib.org/1.3.1/users/event_handling.html)，除了必要的介绍还有几个例子供学习。显然`matplotlib`提供的交互功能与真正的`GUI`包比起来孱弱很多，不过能在作图的过程中加上一两个交互的特性确实是锦上添花的事情。

##链接Event

`matplotlib`里面`event`的用法和`GUI`包的设定大同小异，都需要声明把特定的事件名称和一个函数关联起来：

    :::python
	fig.canvas.mpl_connect('button_press_event',click)

这里`fig`是一个`axes`的实例，链接`event`和函数则要用到`axes`实例的`canvas`属性的`mpl_connect`方法。`'button_press_event'`是鼠标左键点击事件的名称，`click`则是需要调用的函数了。

上面的关联完成以后每次你在画布上点击鼠标都会触发一次`click`这个函数，并同时向`click`传递一个`event`的参数，所以在定义`click`时要注意：

	:::Python
	def click(event):
		...
		ax.figure.canvas.draw()

传递过来的`event`中比较有用的属性有下面几个：

- `x`&`y`：在画布的像素坐标系中鼠标的坐标
- `xdata`&`ydata`：在数据坐标系中鼠标的坐标
- `inaxes`：鼠标所在的`axes`

另外一个需要注意的地方是每次对显示的内容做了更改后都要把图重新画一下，用到下面的语句：

	:::Python
	ax.figure.canvas.draw()


##德劳尼三角化

数学上，德劳尼三角化是针对一个点集通过构造三角形划分空间的一种方法。该方法保证了每个三角形外切圆里面都不含有其他的点（这么说不太严格），实际上使得形成的三角形的所有角都最大化。

由于德劳尼三角化的用途比较广泛，我发现`scipy`已经提供了现成的包`scipy.spatial.Delaunay`，你只需要把点集的坐标送进去，结果就出来了。

官方文档可以参考[这里](http://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.Delaunay.html)。

##代码实现

    :::python
    import matplotlib.pyplot as plt
    import numpy as np
    from scipy.spatial import Delaunay


    #定义一个类来封装数据，避免使用全局变量
    class DrawDelaunay(object):
        def __init__(self):
            self.points = []  # 创建一个空列表用于存放点的坐标
            self.fig = plt.figure()
            self.ax = self.fig.add_subplot(111)
            self.ax.set_xlim([0, 10])
            self.ax.set_ylim([0, 10])
            self.cid = self.fig.canvas.mpl_connect('button_press_event', self.click)

        def click(self, event):  # the function to run everytime click the plane
            self.ax.cla()  # 每次画新图之前先把老的清除，要不然会堆叠在一起
            self.ax.set_xlim([0, 10])
            self.ax.set_ylim([0, 10])

            self.points.append([event.xdata, event.ydata])  # 将每次得到的点击位置加入到列表中
            pointsarray = np.asarray(self.points)  # 将每次得到的点击位置加入到列表中
            if len(self.points) >= 3:
                tri = Delaunay(pointsarray)  # Delaunay这个包只能在多于三个点的情况下工作
                self.ax.triplot(pointsarray[:, 0], pointsarray[:, 1], tri.simplices)  # 把得到的三角形画出来
            self.ax.plot(pointsarray[:, 0], pointsarray[:, 1], 'ro')  # 把每个点也标记出来

            self.ax.figure.canvas.draw()  # 每次都重画一下  


    if __name__ == "__main__":
        draw = DrawDelaunay()
        plt.show()
