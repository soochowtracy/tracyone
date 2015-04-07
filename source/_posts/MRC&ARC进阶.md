title: MRC&ARC进阶
date: 2014-09-23 21:10:00
tags: 
- ios
- 内存
categories: 
- IOS
---


最近公司项目不再需要适配ios5了（3q God），终于可以使用一些比较吊的特性了，比如说Auto Layout，不过呢，这次的主题是在ios5中就有的ARC，乘着这次改版，顺带巩固和学习下ios的内存管理。

###谈谈苹果使用过的内存管理策略

1. 手动引用计数（MRC）：每个对象都有一个retain计数，当retain为0的时候释放对象。
2. 垃圾回收机制（GC）：主要是监视整个对象关系图，查找那些作用域内没有任何引用的对象，然后释放。
3. 自动引用计数（ARC）：也是基于手动引用计数的，不过编译器来管理。

之所以要引入ARC，主要是因为，手动引用计数需要手动进行，增加工作量，开发时需要显示的retain和release，而且容易循环retain；而垃圾回收机制容易产生性能损耗，更何况是在手机上。

<!--more-->

###简要说说MRC

- 给对象发送release消息并不会销毁对象，只有在retain计数为0的时候，才会调用dealloc方法，来释放内存。
- 对使用了retain，copy，alloc，new的任何对象和属性，需要覆盖dealloc方法，使得对象在释放的时候能够先释放这些实例变量。
- autorelease比较常用的几种方式：
1. 方法返回值，由于会retain返回的对象，而又需要释放
2. 在循环中，如果需要创建大量零时对象，建议用autorelease
- 比较常用的Accessor方法有这些：

> -(void)setFoo:(NSString *)foo{
>     if (_foo != foo){
>         [_foo release];
>         _foo = [foo retain];
>     } }
> 
> -(void)setFoo:(NSString *)foo{
>         [_foo retain];
>         [_foo release];
>         _foo = foo; }
> 
> -(void)setFoo:(NSString *)foo{
>         [_foo autorelease];
>         _foo = [foo retain]; }

第二个方法千万不能先release再retain，如果是同一个对象，恰好retain只有1，那么就crash啦。三个方法各有利弊吧，我比较喜欢用第一个。

###内存管理的实现
1. id obj = [NSObject alloc]：

> struct obj_layout{
>     NSUInteger retained; }
> 
> +(id)alloc{
>     int size = sizeOf(struct obj_layout) + 对象大小;
>     struct obj_layout *p = (struct obj_layout *)calloc(1,size);
>     return (id)(p + 1); }

2. [obj retain]：

> -(id)retain{
>     NSIncrementExtraRefCount(self);
>     return self; } 
inline void NSIncrementExtraRefCount(id anObject){
>     ((struct obj_layout *)anObjcet)[-1].retained++; }

3. [obj release]：

>  -(id)release{
>      if(NSDocumentExtraRefCountWasZero(self)){
>             [self dealloc];         
>      }  }  
BOOL NSDocumentExtraRefCountWasZero(id anObject){
>     if(((struct obj_layout *)anObjcet)[-1].retained == 0){
>         return YES;
>     }else{
>         ((struct obj_layout *)anObjcet)[-1].retained--;
>         return NO;
>     } }

4. dealloc：

>  -dealloc{
>     NSDeallocateObject(self); } inline void NSDeallocateObject(id anObject){
>     struct obj_layout *o = &((struct obj_layout *)anObjcet)[-1];
>     free(o); }

总结一下，其实就是在指向对象的指针地址向低地址便宜一个retain结构体的大小，期间存放retain计数，然后alloc和retain＋1，而release如果已经为0则调用dealloc方法，否则retain－1，最终在dealloc中释放掉对象。

###MRC转ARC的难点及步骤
1. Objective-C对象和Core Foundation对象之间的转换，需要恰当的使用CFBridgeRetain/CFBridgeRelease或者_bridge_retained/_bridge_transfer转换。
2. 避免循环引用，block和delegate必须注意weak的使用,NSTimer，还有注册的观察者，在dealloc中需要注销。

第一步：
![Alt text](/pic/PastedGraphic-1.png)


第二步：
![Alt text](/pic/PastedGraphic-2.png)

第三步：修改错误点；
![Alt text](/pic/PastedGraphic-3.png)


###总结：
1. 自己生成的对象，自己持有
2. 非自己生成的对象，自己也能持有
3. 不再需要持有的对象，自己释放
4. 不是自己持有的对象不能释放


















