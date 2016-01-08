title: Storyboard复杂 UI 处理汇总
date: 2016-01-08 16:47:21
categories: iOS
tags: [Storyboard,UIScrollView]
---
<br>
**作者：秋儿（lvruifei@foxmail.com）**

<br>
当我们在用 Storyboard 设计一些复杂的 UI 时，总觉得不是很好处理，约束不太容易正确添加。以下是我在这方面遇到的一些问题及找到的处理方法：

###	在UIScrollView中嵌套多个TableView:
<http://www.jianshu.com/p/bad9bde9b81e>
文中 demo 是基于 Swift 语言做的（还好我刚把 Swift 语法看完，能看懂 demo.😉）

<!-- more -->
<br>
<font color=brown>**对其补充：**</font>

若是页面需要导航栏，第一次进入页面时，第一个 table 会下陷64个像素，上下滑动列表后，回到导航条下面的位置。而第二个table 从最上面向下拉时，scorllview 显示偏移 x = 0 的的位置。
在代码中添加以下代码，可解决这些问题
<br>
<font color=green>**解决方案：**</font>

	self.edgesForExtendedLayout = UIRectEdgeNone