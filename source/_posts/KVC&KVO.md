title: "KVC&KVO"
date: 2014-09-09 21:14:40
tags: 
- ios
categories: 
- IOS
---


###简介
从原理上来说，它的实现根本是OC的动态性和运行时机制，每一门面向对象语言都能在运行时来动态的分配和改变内存。
从设计模式的角度来看，KVC/KVO是观察者模式的一种实现，用来降低 model 与 view 的耦合程度；
从协议的角度看，它的本质是定义了一套让我们去遵守的方法，在NSObject中实现了NSKeyValueCoding/NSKeyValueObserving的**非正式协议**；

<!--more-->
###KVC用法

**1. key & keyValue**
    - (id)valueForKey:(NSString *)key;  
    - (void)setValue:(id)value forKey:(NSString *)key;  
    - (id)valueForKeyPath:(NSString *)keyPath;  
    - (void)setValue:(id)value forKeyPath:(NSString *)keyPath;  

前边两个方法用到的Key较容易理解，就是要访问的属性名称对应的字符串。
后面两个方法用到的KeyPath是一个被点操作符隔开的用于访问对象的指定属性的字符串序列。比如KeyPath address.street将会访问消息接收对象所包含的address属性中包含的一个street属性。其实KeyPath说白了就是我们平时使用点操作访问某个对象的属性时所写的那个字符串

**2. Collection Operators**

    NSNumber *transactionAverage = [transactions valueForKeyPath:@"@avg.amount"]; 
    NSNumber *numberOfTransactions = [transactions valueForKeyPath:@"@count"];  
    
简单集合运算符共有@avg，@count，@max，@min，@sum5种，都表示啥不用我说了吧，目前还不支持自定义

###实现原理

**1.KVC如何访问属性值**

*KVC再某种程度上提供了访问器的替代方案。不过访问器方法是一个很好的东西，以至于只要是有可能，KVC也尽量再访问器方法的帮助下工作。为了设置或者返回对象属性，KVC按顺序使用如下技术：
①检查是否存在-<key>、-is<key>（只针对布尔值有效）或者-get<key>的访问器方法，如果有可能，就是用这些方法返回值；
检查是否存在名为-set<key>:的方法，并使用它做设置值。对于-get<key>和-set<key>:方法，将大写Key字符串的第一个字母，并与Cocoa的方法命名保持一致；
②如果上述方法不可用，则检查名为-__<key>，-_is<key>（只针对布尔值有效）,-_get<key>和-_set<key>:方法；
③如果没有找到访问器方法，可以尝试直接访问实例变量。实例变量可以是名为：<key>或_ _<key>;
④如果仍未找到，则调用valueForUndefinedKey:和setValue:forUndefinedKey:方法。这些方法的默认实现都是抛出异常，我们可以根据需要重写它们。*

**2.KVO实现原理**

当某个类的对象第一次被观察时，系统就会在运行期动态地创建该类的一个派生类，在这个派生类中重写基类中任何被观察属性的 setter 方法。

派生类在被重写的 setter 方法实现真正的通知机制，这么做是基于设置属性会调用 setter 方法，而通过重写就获得了 KVO 需要的通知机制。当然前提是要通过遵循 KVO 的属性设置方式来变更属性值，如果仅是直接修改属性对应的成员变量，是无法实现 KVO 的。

同时派生类还重写了 class 方法以“欺骗”外部调用者它就是起初的那个类。然后系统将这个对象的 isa 指针指向这个新诞生的派生类，因此这个对象就成为该派生类的对象了，因而在该对象上对 setter 的调用就会调用重写的 setter，从而激活键值通知机制。此外，派生类还重写了 dealloc 方法来释放资源。


###KVO和线程

一个需要注意的地方是，KVO 行为是同步的，并且发生与所观察的值发生变化的同样的线程上。没有队列或者 Run-loop 的处理。手动或者自动调用 -didChange... 会触发 KVO 通知。

所以，当我们试图从其他线程改变属性值的时候我们应当十分小心，除非能确定所有的观察者都用线程安全的方法处理 KVO 通知。通常来说，我们不推荐把 KVO 和多线程混起来。如果我们要用多个队列和线程，我们不应该在它们互相之间用 KVO。

KVO 是同步运行的这个特性非常强大，只要我们在单一线程上面运行（比如主队列 main queue），KVO 会保证下列两种情况的发生：

首先，如果我们调用一个支持 KVO 的 setter 方法，如下所示：

    self.exchangeRate = 2.345;

KVO 能保证所有 exchangeRate 的观察者在 setter 方法返回前被通知到。

其次，如果某个键被观察的时候附上了 NSKeyValueObservingOptionPrior 选项，直到 -observe... 被调用之前， exchangeRate 的 accessor 方法都会返回同样的值。














