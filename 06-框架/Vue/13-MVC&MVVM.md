<!--
 * @Author: yaohebin
 * @Date: 2021-02-02 07:58:10
 * @LastEditTime: 2022-07-20 16:45:17
 * @LastEditors: yaohebin
 * @Description: MVC&MVVM
-->

# MVC&MVVM

> **参考**
>
> [《深入理解 MVC》(作者：陈大侠)【来源：知乎】](https://zhuanlan.zhihu.com/p/35680070)
>
> [《MVC 模式介绍》(作者：liitdar)【来源：CSDN】](https://liitdar.blog.csdn.net/article/details/86685880)
>
> [《【MVC 框架】——什么是 MVC 框架》(作者：灰白色可乐)【来源：CSDN】](https://blog.csdn.net/fengyao1995/article/details/46983287)
>
> [《MVC 框架详解（资源整理）》(作者：皓月星辰\_w)【来源：CSDN】](https://blog.csdn.net/qq_21225505/article/details/81666986)
>
> [《什么是 MVC》(作者：zjthorse)【来源：CSDN】](https://blog.csdn.net/zjthorse/article/details/82077964)
>
> [《前后端分手大师——MVVM 模式》(作者：DOM 哥)【来源：博客园】](https://www.cnblogs.com/iovec/p/7840228.html)
>
> [《MVC，MVP 和 MVVM 的图示》(作者：阮一峰)【来源：阮一峰的网络日志】](https://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)
>
> [《MVC、MVP、MVVM 的区别和联系（精讲版）》【来源：C 语言中文网】](http://c.biancheng.net/view/7743.html)

## MVC

- Model：数据模型，用来存储数据
- View：视图界面，用来展示 UI 界面和响应用户交互
- Controller：控制器(大管家角色)，监听模型数据的改变和控制视图行为、处理用户交互

业务越来越复杂时，由于 Controller 主要用来处理各种逻辑和数据转化，复杂业务逻辑界面的 Controller 非常庞大，维护困难。

## MVVM

- Model：数据模型，用来存储数据
- View：视图界面，用来展示 UI 界面和响应用户交互
- ViewModel

MVVM 的核心是 ViewModel 层，它就像是一个中转站（value converter），负责转换 Model 中的数据对象来让数据变得更容易管理和使用，该层向上与视图层进行双向数据绑定，向下与 Model 层通过接口请求进行数据交互，起呈上启下作用。

ViewModel 是由前端开发人员组织生成和维护的视图数据层。在这一层，前端开发者对从后端获取的 Model 数据进行转换处理，做二次封装，以生成符合 View 层使用预期的视图数据模型。

需要注意的是 ViewModel 所封装出来的数据模型包括视图的状态和行为两部分，而 Model 层的数据模型是只包含状态的，比如页面的这一块展示什么，那一块展示什么这些都属于视图状态（展示），而页面加载进来时发生什么，点击这一块发生什么，这一块滚动时发生什么这些都属于视图行为（交互），视图状态和行为都封装在了 ViewModel 里。这样的封装使得 ViewModel 可以完整地去描述 View 层。

由于实现了双向绑定，ViewModel 的内容会实时展现在 View 层，这是激动人心的，因为前端开发者再也不必低效又麻烦地通过操纵 DOM 去更新视图，MVVM 框架已经把最脏最累的一块做好了，我们开发者只需要处理和维护 ViewModel，更新数据视图就会自动得到相应更新，真正实现数据驱动开发。

看到了吧，View 层展现的不是 Model 层的数据，而是 ViewModel 的数据，由 ViewModel 负责与 Model 层交互，这就完全解耦了 View 层和 Model 层，这个解耦是至关重要的，它是前后端分离方案实施的重要一环。
