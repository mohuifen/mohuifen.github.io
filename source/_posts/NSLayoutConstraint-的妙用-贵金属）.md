title: NSLayoutConstraint 的妙用(贵金属）
date: 2015-12-22 12:01:35
categories: iOS
tags: [贵金属]
---


<br>
**时间：2015年12月22日**

**作者：秋儿（lvruifei@foxmail.com）**

<br>
贵金属项目，是第一次使用 storyboard 开发，以下是初次接触 NSLayoutConstraint，遇到的问题：

<!-- more -->
####	问题1：
用 storyboard 搭建的 UI，在 tableView 的 header 中的控件的高度是不固定的，条件1下控件高90，条件2下控件高45,所以就考虑用代码控制控件的高度。

在用代码设置了控件的高度和 header 的高度后，发现效果并非如预期所想。在 storyboard 里面设置了90，在代码中设置了45，显示时还是90，代码更改高度没变化。

<font color=brown>**原因：**</font>UIButton 的 buttonType
在 storyboard 里面设置了控件的高度，并且添加了约束，其中便有高度的约束，约束有了，代码更改控件的高度，并不会有效果。


<font color=green>**解决方案：**</font>
NSLayoutConstraint 是可以设置成属性的，在 storyboard 中添加控件高度约束的属性的连线，连接到相对应的文件中。在文件中更改该属性的值，如下：

	@property (weak, nonatomic) IBOutlet NSLayoutConstraint *surplusHeightLayout;

	
	self.surplusHeightLayout.constant = 45;

####	问题2：
用 storyboard 搭建的 UI，控件之间有分割线，UI设计师设计的是1像素，所以需要设置0.5高。但是在Xcode7 的 storyboard 中无法设置带有".5"的数字。虽然可以像上面的问题的解决方案那样处理，可以达到效果，但是分割线太多，那样处理工作量太大，还会有很多冗余的代码。

<font color=green>**解决方案：**</font>
 创建 NSLayoutConstraint 的子类NSLayoutConstraintHairline，在.m 中添加以下代码：


	- (void)awakeFromNib {
        [super awakeFromNib];
        if (self.constant == 1) {
           self.constant = 0.5/[UIScreen mainScreen].scale;
        }
	}

在 storyboard 中设置分割线的高为任意值，选中分割线的高的约束，修改其类型NSLayoutConstraint为创建的NSLayoutConstraintHairline即可达到目的。
