< [Back](README.md)

Objective-C 开发经验和技巧
======================


## 什么样的架构叫好架构？

   * 代码整齐，分类明确，没有common，没有core

   * 不用文档，或很少文档，就能让业务方上手

   * 思路和方法要统一，尽量不要多元

   * 没有横向依赖，万不得已不出现跨层访问

   * 对业务方该限制的地方有限制，该灵活的地方要给业务方创造灵活实现的条件

   * 易测试，易拓展

   * 保持一定量的超前性

   * 接口少，接口参数少

   * 高性能


## 使用 Category 或是基类

这个部分不是很好给出代码实例，要实例的话想想当初做各级员工结算工资的课程设计吧，只作简要说明。

在逻辑比较繁杂，某个类代码量非常庞大时，可以考虑使用基类，将公有的属性和方法在基类中实现，自己在使用时只需要关注一些单独的逻辑即可，可以大大提高代码效率。

使用 category 是对某个类进行一些简单的扩展，在 category 中定义的方法等同于类的方法，是一样的，为了让功能划分更纯粹一些，一言以蔽之，强迫症。。

给出一个小场景：比如我定义了一个 UITableViewCell 的类，在 A，B，C 的视图控制器中样式都是一样的，但是在D 的视图控制器中需要对它进行一些改造，这样的话就可以在 D 的类中加上一个 Category来进行操作，在 D 中直接调用这个方法即可

```Objective-C
// DViewController.h
@interface DViewController{
}
 
@interface ModuleCell(diff)
-(void)diffTheCellByParam:(id)param;

// DViewController.m
@implementation DViewController
 
@end
#pragma mark - 
@implementation ModuleCell(diff)
-(void)diffTheCellByParam:(id)param{
    // Do something
}
```


## 使用 MVC
MVC 模式是最常见的模式，而且在学习的第一时间就有接触，可能有些人看到这儿觉得，你这不是废话？这谁不知道。

但是我发现在 Objective-C 这个大家庭中，这个模式是遵守的最差的，相信你们一定见过在 ViewController 中写完所有的数据处理，UI 渲染，视图跳转各类动画的经典案例，因为我们就是这类型创造者，新手应该都是这样过来的（只是说明是一个大概率事件，很多资深其他平台开发者转过来时这方面是做的非常好的）。

把数据的处理（获取，筛选，排序等）工作放在 Model 中，所谓 Model，新建一个继承自 NSObject（一般是 NSObject） 的类，用来处理数据，直接调用即可；

把视图的渲染放在 View 中（简单的视图加载可直接在 Controller 中完成），在遇到比较繁杂，需要几百行代码来完成时，如果你是用 xib或是 storyboard 配合代码，这部分无需这么严格，公用的视图必然是放在 View 中了；

视图控制器 Controller 中只用来调用数据，显示数据，视图的加载，跳转等工作。

视图内容在繁杂时可以考虑使用多个视图控制器联合控制一个页面，如 Apple 的 TabBarController 的工作机理。

将视图以 AddChildViewController 的方式添加到当前视图。

在 A 控制器中添加 B 控制器的视图，这样 A 同时包含了 A 和 B 的视图，B 视图中得 UI 逻辑依然在 B 中进行处理

```Objective-C
BViewController *secondVC = VCFromSB(storyboardPatient, @"XRPatientAllVC");
[self.view addSubview:secondVC.view];
[self addChildViewController:secondVC];
[secondVC didMoveToParentViewController:self];// 将B 视图的 UI 响应事件移到 A 中，如果不这样操作，只要点击B 视图中得按钮或是滚动 table 就会崩溃

```

案例不是太好写，要写得好长一篇文章了，我还得想一项目，可以参考这部分文章：
[更轻量的 ViewControllers](http://objccn.io/issue-1/)  
