---
layout:     post
title:      完全自定制的AlertView，想怎么玩就怎么玩O(∩_∩)O哈哈~
subtitle:   完全自定制的AlertView，想怎么玩就怎么玩O(∩_∩)O哈哈~
date:       2020-04-16
author:     Paul
header-img: img/post-bg-debug.png
catalog: true
tags:
- View
- iOS
--- 

GitHub下载：[WrapAlertView](https://github.com/PaulPaulBoBo/WrapAlertView)

## 背景
对话框对于APP来说是比较常用的控件之一。系统提供了基础的对话框类UIAlertView以及后来高版本新加的UIAlertController，可以满足基本的APP需求。

但是随着APP内容的丰富，以及设计的多样化，弹出框的设计样式也层出不穷，所以自定制的对话框便越来越常用。

每次用到单独写对话框显然不合适，因为有很多重复性的工作，既浪费精力，又效率低下。所以把这些重复性的工作抽象出来，封装成工具类可以很好的提高开发效率。

## WrapAlertView 完全自定制
如下图所示，是几种定义好的样式，用的时候可以直接调用。

#### 先来看一个调用的例子

该样式可自适应内容，当内容过多时，可滑动查看。

![pic](https://github.com/PaulPaulBoBo/docs/blob/master/img/3061217-fb84aba3b482b275.PNG?raw=true)

    [[WrapAlertView shareAlertView] showAlertViewBlock:^(UIView *alertView) {
                    WrapCustomView *view = [[WrapCustomView alloc] initSimple];
                    view.titleLabel.text = @"UIAlertControllerStyleAlert";
                    view.contentLabel.text = @"asdasdadasdasdjkahdkahlksjhldakjshdlkjhks\n123djfhaksjdhfkahdkf\nhsddasdadasdasdjk456ahsdadasdasdjkahdkahlksjhldakjshdlkjhks\n123djfhaksjdhfkahdkf\nhsddasdadasdasdjk456ahsdadasdasdjkahdkahlksjhldakjshdlkjhks\n123djfhaksjdhfkahdkf\nhsddasdadasdasdjk456ahsdadasdasdjkahdkahlksjhldakjshdlkjhks\n123djfhaksjdhfkahdkf\nhsddasdadasdasdjk456ahsdadasdasdjkahdkahlksjhldakjshdlkjhks\n123djfhaksjdhfkahdkf\nhsddasdadasdasdjk456ahsdadasdasdjkahdkahlksjhldakjshdlkjhks\n123djfhaksjdhfkahdkf\nhsddasdadasdasdjk456ah\ndkahlksjhldakjshdlkjhksdjfhaksjd\nhfkahdkfhsddasdadasdasdjka\nhdkahlksjhldakjshdlkjhksdjfhaksjdhfkahdkfhsddasdadasdasdjkahdkahlksjhldakjshdlkjhksdjfhaksjdhfkahdkfhsddasdadasdasdjkahdkahlksjhldakjshdlkjhksdjfhaksjdhfkahdkfhsd000";
                    [view.cancelBtn addTarget:self action:@selector(cancelBtnAction:) forControlEvents:(UIControlEventTouchUpInside)];
                    [view.sureBtn addTarget:self action:@selector(sureBtnAction:) forControlEvents:(UIControlEventTouchUpInside)];
                    [alertView addSubview:view];
                }];
如果想要自定制弹出框的视图，只需在block中创建视图并添加到alertView上即可。

不用担心你的视图会超出屏幕，视图在回调完成后会自适应子视图。如果过长，会采用滑动视图的方案来容纳。

#### 关于自定义视图的实现方式

如果是习惯写代码搭建视图的同学，直接在block中创建就可以。

此处的WrapCustomView采用Xib方式创建，这种方式更直观，更高效。此处要提醒一下，使用Xib会出现一个问题，解决方案请参考 [这里](https://www.jianshu.com/p/36fe429757d3)。

## 设计目标

1.允许完全自定制view样式。
2.自适应内容宽度和高度。
3.使用单例访问。
4.拥有几种默认的样式。

## 设计思路
alert的样式变得是在子视图的样式，不变的是承载页，也就是windows层。所以为了实现完全自定制，子视图就要放到外面创建，也就是说封装起来的是承载页以及一些不变的元素。

    @property (nonatomic, strong) UIWindow *WR_window; //windows层
    @property (nonatomic, strong) UIView *WR_blackView; //底层透明黑色视图层
    @property (nonatomic, strong) UIView *WR_backView; //可操作视图
    @property (nonatomic, assign) BOOL WR_isVisible; //是否已经显示

还有几个操作方法

    /**
    单例

    @return 视图本身
    */
    +(WrapAlertView *)shareAlertView;
    
    /**
     显示视图，block中实现自定制视图，指定类型
    
     @param type 类型WrapAlertViewType
     @param block 调用处实现的代码块
     */
    -(void)showType:(WrapAlertViewType)type block:(CreateWrapAlertViewBlock)block;
    
    /**
     显示视图，block中实现自定制视图 Alert
    
     @param block 调用处实现的代码块
     */
    -(void)showSheetViewBlock:(CreateWrapAlertViewBlock)block;
    
    /**
     显示视图，block中实现自定制视图 Sheet
     
     @param block 调用处实现的代码块
     */
    -(void)showAlertViewBlock:(CreateWrapAlertViewBlock)block;
    
    /**
     隐藏视图
     */
    -(void)hide;
    
    /**
     是否允许点击透明黑色背景视图隐藏界面
    
     @param canHideByTapBlack YES-允许， NO-不允许，默认YES
     */
    -(void)canHideByTapBlack:(BOOL)canHideByTapBlack;


其中有个block参数CreateWrapAlertViewBlock，定义如下

    typedef void(^CreateWrapAlertViewBlock)(UIView *alertView);

## 效果图
![](https://github.com/PaulPaulBoBo/docs/blob/master/img/3061217-93ed88a1fedb0453.PNG?raw=true)
![](https://github.com/PaulPaulBoBo/docs/blob/master/img/3061217-b9c51e3df9ed082b.jpeg?raw=true)

详细代码请至GitHub下载：[WrapAlertView](https://github.com/PaulPaulBoBo/WrapAlertView)
如有问题请留言，谢谢！
