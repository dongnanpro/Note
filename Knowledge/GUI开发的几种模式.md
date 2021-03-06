GUI开发的几种模式
===============
简单记录下几种GUI开发的方式，即时对之前学习的总结，也是对之后GUI开发的一个框架性指导。以后会通过开发继续更新完善GUI开发的框架模式终结。

#使用系统原生API
* 直接调用系统绘图API,如直接使用OpenGL绘制图形。这种方法十分原始，暂时不做讨论。

#使用GUI框架
* 直接使用GUI框架接口绘制界面。这种方式十分常见，但是效率比较低，对于动态的界面比较合适，如果用来编写复杂的静态页面则显得比较啰嗦。
* 使用可视化GUI编辑器。这也是目前很流行的开发方式，通过界面编辑器可以快速构架静态页面，所见即所得，让编辑器生成GUI代码，效率大幅度提升，对动态页面显得力不从心。
* 使用类CSS控制界面属性。随着web的流行，用xml等script开发界面已经成为趋势。类css介于调用框架API绘制界面和用xml绘制界面的两种方法之间，类css只是控制界面的样式，而界面的控件还是需要通过使用api生成，代表性的就是Qt的stylesheet。
* 使用类似xml的绘制界面，通过id等属性和代码联系在一起。代表框架有Qt的qml，Android的界面开发xml。

#在GUI框架下的UI组织方式
* 直接堆叠控件。这是最普通也是最常见的方式，调用框架的API，把它们按照需要组合在一起，再加上事件或消息处理代码，一个UI就完成了。
* MVC模式。这也是使用广泛的模式，而且有很多变种，如Qt的MV模式，C变成了delegate；还有MV加adapter模式。这些原理都是类似的，通过分离数据和视图，然后在视图和数据间创建一个桥梁delegate或adapter，代码可以在运行时从数据生成视图，也可以从视图修改数据。这种方式避免了重复编写相同的视图代码，更重要的时可以随时替换新的视图和数据，只要它们兼容delegate的接口，那么转换仍可以正常进行。
* Graphics/View框架。这个框架用来存放显示二维图形元素，处理那些对图形元素进行操作的交互命令，和MVC明显不同的是这个不需要Model，因为该框架重点是绘图和动画。

##Qt的MV框架简介
Model/View 框架中的数据集（dataset）由若干个数据项（item）组成，每个数据项除了存放应用程序所以要显示的数据外，还放着框架中其他部分需要访问的数据，比如显示颜色。无论数据集以什么数据结构存放，模型总是将它看做一颗树，每个树节点就是一个数据项。列表数据集被看做只含最顶层节点、不含任何子节点的树。
『视图』负责绘制总体外观并处理用户的交互命令，但它 **并不具体负责每个数据项的绘制与编辑**。对于具有列表或者表格结构的数据集，视图计算各数据项的位置，绘制每列上方或者左侧的标头。
『委托』负责绘制每个数据项，并创建编辑器控件对数据项进行编辑。
![MV框架图][1]

##类xml方式绘制UI
使用类xml方式绘制UI，代码的组织和HTML+CSS相似。以标签方式创建控件，并用标签属性设置控件属性，其中也包含了控件样式，样式可以直接写在属性中，也可以引用外部写好的属性，达到公用的目的。子元素可以继承父元素属性，减少重复的代码编写。自己写style的时候也可以继承其他style，达到代码重用的目的。

一个xml界面引用另一个xml控件，xml控件通过状态改变实现动态效果。

#具体的UI摆放方式
最方便实用的就是gridlayout，使用网格布局，可以把控件放到指定的格子内，并且指定控件占几个格子，处于格子的哪个角落以及填充格子的垂直方向或水平方向或全部。指定stretch(Qt)或weigth{x|y}(Java)确定整个窗口拉伸时的控件拉伸的比例。sizeHit(Qt)或getPreferredSize(Java)可以指定最佳控件尺寸，底层的还有boundingRect和shape两个防止绑定控件外围尺寸。

[1]: image/MV.png