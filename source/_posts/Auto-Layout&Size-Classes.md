title: "Auto Layout&Size Classes"
date: 2014-10-05 12:38:08
tags: 
- ios
- auto layout
categories: 
- IOS
---

###简介

我感觉这是一个划时代的布局计数，真正的把机器语言转换成了符合人类的语言。在过去，我们布局都是去设定这个控件的起始坐标和宽高，这其实是很不友好的，比如说，你从来不会说我家的沙发是在客厅的（0，0）坐标开始，往东长3米，往南长2米，这种很奇怪的描述位置的话。更多的你会选择说，靠墙，1米宽，等这种很符合人类思考的话描述。

<!--more-->

应该说Auto Layout其实已经解决了绝大数布局上的问题，不过呢，在某些方面，也有一定的局限性。比如说，在竖屏的时候你想要展示一个tableView，而在横屏时，由于屏幕变宽，想展示collectionView，那么Auto Layout是很难解决这个问题的。也因此，在这次IOS8中新加入了一个成员Size Classes。

SizeClass解决了那些差异化设计，并且很简单的能适配ipad，watch等，甚至一行代码都不需要写。不再根据设备屏幕的具体尺寸来进行区分，而是通过它们的感官表现，将其分为普通 (Regular) 和紧密 (Compact) 两个种类 (class)。开发者便可以无视具体的尺寸，而是对这这两类和它们的组合进行适配。

###Auto Layout

####代码实现

    NSDictionary *viewsDictionary =
                    NSDictionaryOfVariableBindings(self.button1, self.button2);
    NSArray *constraints =
            [NSLayoutConstraint constraintsWithVisualFormat:@"[button1]-[button2]"
                                options:0 metrics:nil views:viewsDictionary];
                                
再举几个可视化语法例子：
- [aButton(50)]-50-[bButton(50)]
a按钮宽50，b按钮宽50，间距50
- [aButton(>=50@1000)]
aButton宽度大于等于50，优先级为1000
- V:[aButton][bButton(==aButton)]
竖直布局，a，b两个button等高
- H:|-[aButton]
水平布局，aButton与左边距默认距离

####一点点心得

在考虑布局时，不应该再和以前一样先去考虑初始坐标，长宽等，其实UI设计描述的语句基本是正确的，比如，这两个Button等宽等等，这就是一种约束。

多个组件布局时可以考虑把几个零散的组件放到同一个view中，然后再用view和其他组件建立约束。

布局时先从一些比较固定的组件入手，从简单到复杂，在设计动画的时候不要忘了加 [view layoutIfNeeded]。

####流程
使用AutoLayout之后，把view显示到屏幕上面大体分成3步。
- Update constraints
- Layout views
- Display

一般来说layoutSubviews负责布局，比如调整View之间的距离，大小，drawRect负责绘制，比如使用什么颜色。而AutoLayout则是在layout之前增加了一个设定约束的过程,也就是update constraints。


###总结
总的来说，iOS对UI这块的改动是跨时代性的，Autolayout的出现使得布局的复杂度减少到了View与View的关系上，再由根 View（也就是屏幕）指定frame，随后所有子View相对布局，把frame的概念归一化到根View的frame上；但有了Size Class后，根视图的frame概念也被移除了，这下整个app的UI和frame这个单词已然脱离关系，这也正是apple想要达到的目的。











