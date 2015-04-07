title: 优秀的ios7实践：天气应用学习（上）
date: 2014-08-06 20:18:05
tags: 
- ios
categories: 
- IOS
---

###写在开头的话
整整三个礼拜，曾自信的以为能够翻译全文的，结果还是只完成了上半部分，中间断断续续，不管怎么说还是坚持了下来。就是图片没有，可以参考[译文](http://www.raywenderlich.com/55384/ios-7-best-practices-part-1) 

发现看懂英文和翻译成比较顺口的文章还是有差别的，希望以后能够大大的提高自己的英语口语水平。

还有上次说的ruby计划已经泡汤，不知道合适才能重拾ruby，下一次准备发一篇对设计模式的理解，和最近再研究的**reactivecocoa**

[译文](http://www.raywenderlich.com/55384/ios-7-best-practices-part-1) 作者：Ryan Nystrom

<!--more-->
每个开发者对于如何去创建一个优秀的ios应用都有他们的想法。有些开发者利用自动布局，有些喜欢用代码写ui，甚至有些喜欢用vim来编码。

随着最近ios7和xcode5升级，我觉得是时候提供一个教学用多种方法和工具创建一个基础的天气应用；你可以把这看成是我最好的一次ios7实践。ios先驱开发者习惯创建一个待办应用，然而新生代希望像ios的更新一样娴熟的用新的技术，例如，数据管理和网络请求等。

 在这个教程中，你会学会怎么利用下列工具和知识创建你自己的app：
- Cocoapods
- manual layout in code
- ReactiveCocoa
- OpenWeatherMap

这个课程是为那些有一定基础但还没踏入太多高等领域的进阶开发者设计的。它对那些想探索OC实用程序也是一个好的开始。

##开始
打开Xcode到**File\New\Project**。选择**Application\Empty Application**。项目名字     叫**SimpleWeather**，点击**Next**，再选一个目录存放，最后点**Create**。

现在你搭好了基础的项目，下一步开始搭建第三方库，不过先保证你的Xcode是关闭的而不会影响接下来的步骤。

###Cocoapods
你将会用**[Cocoapods](http://cocoapods.org)**管理那些下载的代码，添加文件到你的项目和配置任何你项目需要的设置。让我们先来看看你需要那些项目。

###Mantle
**[Mantle](https://github.com/Mantle/Mantle)** 是由Github团队编写用来清除在oc中nsobject转换成json的样板代码。Mantle也可以把简单的json值转换成复杂的例如NSdate，NSURL，甚至一些自定义的类。

###LBBlurredImage

**[LBBlurredImage](https://github.com/lukabernardi/LBBlurredImage)**是一个用来处理图片模糊的UIImageView的扩展。你能用一行简单的代码达到模糊效果，如果想知道原理，下载[源码](https://github.com/lukabernardi/LBBlurredImage/blob/master/LBBlurredImage/UIImageView%2BLBBlurredImage.m#L25-88)。

###TSMessages
**[TSMessages](https://github.com/toursprung/TSMessages)** 是又一个用来弹出显示警告和通知的轻量级库。当你呈现的错误信息不够清晰的时候，展示一层透明警告栏而不是模板试图（例如 UIAlertView）来尽可能减少用户的反感。

你可以在网络连接失败或者APl引起的其他错误时使用TSMessages。如果出了任何问题，你会看到：

###ReactiveCocoa

你会用到最后一个也是由Github团队编写的库就是**[ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)**。它带来了就像.NET中的Reactive Extensions一样在oc 中的一种设计模式，你将会花大量时间来学习它。

##设置你的Cocoapods库

要搭建Cocoapods库，先要确保你已经安装了，为此打开终端，打下列命令然后敲打回车。
> which pod

你会看到类似下列信息：
> /user/bin/pod

这取决于你如何管理你的Ruby gems，比如说，如果你用[rbenv](http://rbenv.org)或者[RVM](http://rvm.io)那么你的路径可能略有不同。

如果终端返回的是**pod not found**，说明Cocoapods还没安装；按照我们的[Cocoapods教程](http://www.raywenderlich.com/64546/introduction-to-cocoapods-2)安装，如果你想学习更多那也是一个很好的资源。

[Podfiles](http://guides.cocoapods.org/syntax/podfile.html)用来告诉Cocoapod应该包括哪些Pods，开源项目。

要创建你第一个Cocoapod项目，在终端先用**cd**命令切换到存有你的xcode文件夹。在终端中用vim Podfile写下下列命令：
> platform :ios, '7.0'
pod 'Mantle'
pod 'LBBlurredImage'
pod 'TSMessages'
pod 'ReactiveCocoa'

这个文件会做两件事：
- 告诉Cocoapods你目标版本是哪个，现在这个是ios 7.0。
- 它也告诉Cocoapods你要引入的一系列项目。

打下列命令来安装四个Pods：
> pod install

耐心的等待pod安装大量的包，最终你的终端会显示下列信息：
> Analyzing dependencies
Downloading dependencies
Installing HexColors (2.2.1)
Installing LBBlurredImage (0.2.0)
Installing Mantle (1.5)
Installing ReactiveCocoa (2.3.1)
Installing TSMessages (0.9.9)
Generating Pods project
Integrating client project
[!] From now on use `SimpleWeather.xcworkspace`.

Cocoapods会在你的项目目录中创建一个新的分支；不过你只需要关心**SimpleWeather.xcworkspace**.

打开**SimpleWeather.xcworkspace**，你会看到每个你引入的库和pods项目：

如下图一样确保你选中了**SimpleWeather**项目：

运行一下以确保每件事都顺利进行着：


现在看上去好像不太对，不过你马上就会添加一些内容。

> **Note:** 你可能会发现一些警告，那是因为项目中引入了一些其他开发者的库，每个开发者对于警告都有自己的忍耐程度。大多数时间只需要忽略它们，只需要确保没有编译错误！

##创建你的主试图

应用看上去很复杂，其实也就一个试图控制器，接下来你会增加一个。

选中项目，点击**File\New\File**然后选择**CocopTouch\Objective-C**，取名**WXController**，继承**UIViewController**。

确保**Targeted for iPad** 和 **With XIB for user interface**都没选：

打开**WXController.m** 在**-viewDidLoad**方法中如下重写：

>  -(void)viewDidLoad{
    [super viewDidLoad]
    self.view.backgroundColor = [UIColor redColor];
}

然后打开**AppDelegate.m**，引入下列两个类。
> import "WXController.h"
import <TSMessage.h>

眼尖的读者会发现**WXController**用了双引号而**TSMessage**却是尖括号，有什么区别呢？

回想下在创建Podfile时,你用Cocoapods引入TSMessage。Cocoapods创建了TSMessage项目并加到你的工作区。如果你是从工作区引入其他项目，只需要用尖括号。
用下列代码代替 **-application:didFinishLaunchingWithOptions:**
> -(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // 1
    self.window.rootViewController = [[WXController alloc] init];
    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    // 2
    [TSMessage setDefaultViewController: self.window.rootViewController];
    return YES;
}

随着数字注释你会发现：

1. 初始化并把**WXController**设为application的根试图。通常都是一个**UINavigationgController**或者**UItabBarController**，不过在这个应用中，你将用到**WxController**。
2. 设置默认的试图控制器给TSMessage，这样你就不需要经常选择哪个去呈现警告。

状态栏在红色的背景中很不清晰，还好有一种比较简单的方法改善。
在ios7的**UIViewController**中有个新接口控制状态栏的外观，打开**WXController**在**-viewDidLoad**下面写下这些代码：
> -(UIStatusBarStyle)preferredStatusBarStyle {
    return UIStatusBarStyleLightContent;
}

运行一下你会发现状态栏变成白色了。

##设置App的试图

是时候实现你的app了，下载必要的图片然后放在合适的地方，这些图片是由Flickr和Dribbble提供的。

> **Note:**背景图片是San Francisco的景象，因为这个应用定位的就是这里，你也可以自由定制你的北京图片，比如你的家乡还有其他。

回到**Xcode**添加图片到app，选中**Copy items into destination group's folder(if needed)**。

打开**WXController.h**添加以下代理协议：
> <UITableViewDataSource, UITableViewDelegate, UIScrollViewDelegate>

打开**WXController**，你可以通过cmd+ctrl+up在.h,.m文件快速切换。

引入下列文件：

> ＃import <LBBlurredImage/UIImageView+LBBlurredImage.h>

**LBBIurredImage.h**在Cocoapods中，你会用它来处理你的背景图片。

在引入下面添加几个空的私有属性：

> @interface WXController ()
@property (nonatomic, strong) UIImageView *backgroundImageView;
@property (nonatomic, strong) UIImageView *blurredImageView;
@property (nonatomic, strong) UITableView *tableView;
@property (nonatomic, assign) CGFloat screenHeight;
@end
 
是时候创建图片了，也许你会问**IBoutlets**在哪？没关系，我们都会用代码来实现。

等等，别发飙，其实有很多种方法来创建视图个人有各自的偏好，Storyboard,NIBs还有code都有反对和支持者。

这个应用的视图不是很复杂，也没有什么触发事件会导致自动布局异常，而且这个应用是用来学习的，还是选择代码布局。

你会创建三层视图来达到一开始你看的那种动画效果，下面是一个模型，tableview的外观会改变。


为了实现动态模糊效果，在滑动视图的时候需要改alpha

打开**WXController**用下列代码替换** viewDidLoad**中的来设置背景颜色：

    // 1
    self.screenHeight = [UIScreen mainScreen].bounds.size.height;
     
    UIImage *background = [UIImage imageNamed:@"bg"];
     
    // 2
    self.backgroundImageView = [[UIImageView alloc] initWithImage:background];
    self.backgroundImageView.contentMode = UIViewContentModeScaleAspectFill;
    [self.view addSubview:self.backgroundImageView];
     
    // 3
    self.blurredImageView = [[UIImageView alloc] init];
    self.blurredImageView.contentMode = UIViewContentModeScaleAspectFill;
    self.blurredImageView.alpha = 0;
    [self.blurredImageView setImageToBlur:background blurRadius:10 completionBlock:nil];
    [self.view addSubview:self.blurredImageView];
     
    // 4
    self.tableView = [[UITableView alloc] init];
    self.tableView.backgroundColor = [UIColor clearColor];
    self.tableView.delegate = self;
    self.tableView.dataSource = self;
    self.tableView.separatorColor = [UIColor colorWithWhite:1 alpha:0.2];
    self.tableView.pagingEnabled = YES;
    [self.view addSubview:self.tableView];

这是相当简单的一段代码：
1. 保存屏幕高度，在接下来呈现天气数据的时候需要用到。
2. 创建一个静态的背景图片，添加到视图。
3. 用LBBLurredImage创建一个模糊背景图，设置alpha为0以至于背景图片是可见的。
4. 创建一个tableview来呈现数据，WXController是代理者，也是scrollview的，注意**pagingEnabled**设置成YES。

在**WXController**的**@implementation**部分增加下列代码来实现UITableView的代理

    // 1
    #pragma mark - UITableViewDataSource
     
    // 2
    - (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
        return 2;
    }
     
    - (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
        // TODO: Return count of forecast
        return 0;
    }
     
    - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
        static NSString *CellIdentifier = @"CellIdentifier";
        UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
     
        if (! cell) {
            cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleValue1 reuseIdentifier:CellIdentifier];
        }
     
        // 3
        cell.selectionStyle = UITableViewCellSelectionStyleNone;
        cell.backgroundColor = [UIColor colorWithWhite:0 alpha:0.2];
        cell.textLabel.textColor = [UIColor whiteColor];
        cell.detailTextLabel.textColor = [UIColor whiteColor];
     
        // TODO: Setup the cell
     
        return cell;
    }
     
    #pragma mark - UITableViewDelegate
     
    - (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
        // TODO: Determine cell height based on screen
        return 44;
    }

即使上述代码有些是描述性的，你也能学到一些：
1. Pragma marks 能够很好的帮助组织代码。
2. 你的列表分两段，一段用来播报每小时预告，一段放每日信息，所以每次都返回两段。
3. 预报的cell不能被选中，给它们透明黑的背景和白色的内容。

> 注意：用标准话的注释。**// TODO:**帮助Xcode找到你需要完成的代码，甚至你还能用Ctrl－6来看TODO元素。

最后在**WXControlle.m**中加入下列代码：

    - (void)viewWillLayoutSubviews {
        [super viewWillLayoutSubviews];
     
        CGRect bounds = self.view.bounds;
     
        self.backgroundImageView.frame = bounds;
        self.blurredImageView.frame = bounds;
        self.tableView.frame = bounds;
    }
    
试图控制器用上述代码来布局**WXController.m**的子试图。建立并运行来看看你的试图。


仔细看，你会发现有些一些不透明的分割线。仍然在 **-viewDidLoad**加入下列代码来布局：

    // 1
    CGRect headerFrame = [UIScreen mainScreen].bounds;
    // 2
    CGFloat inset = 20;
    // 3
    CGFloat temperatureHeight = 110;
    CGFloat hiloHeight = 40;
    CGFloat iconHeight = 30;
    // 4
    CGRect hiloFrame = CGRectMake(inset, 
                                  headerFrame.size.height - hiloHeight,
                                  headerFrame.size.width - (2 * inset),
                                  hiloHeight);
     
    CGRect temperatureFrame = CGRectMake(inset, 
                                         headerFrame.size.height - (temperatureHeight + hiloHeight),
                                         headerFrame.size.width - (2 * inset),
                                         temperatureHeight);
     
    CGRect iconFrame = CGRectMake(inset, 
                                  temperatureFrame.origin.y - iconHeight, 
                                  iconHeight, 
                                  iconHeight);
    // 5
    CGRect conditionsFrame = iconFrame;
    conditionsFrame.size.width = self.view.bounds.size.width - (((2 * inset) + iconHeight) + 10);
    conditionsFrame.origin.x = iconFrame.origin.x + (iconHeight + 10);
    
这是一些很常规的设置，下面是注释：
1. 设置列表和屏幕一样大，列表分成头，每日，和每小时预报是有好处的。
2. 创建一个边距变量用来更好的布局你的标签。
3. 创建一系列用来初始化试图的高度变量，设为常量来方便的配置和改变你试图。
4. 用这些常量创建你标签和图标的大小。
5. 拷贝这些图标边框，调整到内容能够有一些扩展的空间，放到原有图标的右边，你会发现这些布局是怎么起作用的。

把下列代码拷贝到**-viewDidLoad**中：

    // 1
    UIView *header = [[UIView alloc] initWithFrame:headerFrame];
    header.backgroundColor = [UIColor clearColor];
    self.tableView.tableHeaderView = header;
     
    // 2
    // bottom left
    UILabel *temperatureLabel = [[UILabel alloc] initWithFrame:temperatureFrame];
    temperatureLabel.backgroundColor = [UIColor clearColor];
    temperatureLabel.textColor = [UIColor whiteColor];
    temperatureLabel.text = @"0°";
    temperatureLabel.font = [UIFont fontWithName:@"HelveticaNeue-UltraLight" size:120];
    [header addSubview:temperatureLabel];
     
    // bottom left
    UILabel *hiloLabel = [[UILabel alloc] initWithFrame:hiloFrame];
    hiloLabel.backgroundColor = [UIColor clearColor];
    hiloLabel.textColor = [UIColor whiteColor];
    hiloLabel.text = @"0° / 0°";
    hiloLabel.font = [UIFont fontWithName:@"HelveticaNeue-Light" size:28];
    [header addSubview:hiloLabel];
     
    // top
    UILabel *cityLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 20, self.view.bounds.size.width, 30)];
    cityLabel.backgroundColor = [UIColor clearColor];
    cityLabel.textColor = [UIColor whiteColor];
    cityLabel.text = @"Loading...";
    cityLabel.font = [UIFont fontWithName:@"HelveticaNeue-Light" size:18];
    cityLabel.textAlignment = NSTextAlignmentCenter;
    [header addSubview:cityLabel];
     
    UILabel *conditionsLabel = [[UILabel alloc] initWithFrame:conditionsFrame];
    conditionsLabel.backgroundColor = [UIColor clearColor];
    conditionsLabel.font = [UIFont fontWithName:@"HelveticaNeue-Light" size:18];
    conditionsLabel.textColor = [UIColor whiteColor];
    [header addSubview:conditionsLabel];
     
    // 3
    // bottom left
    UIImageView *iconView = [[UIImageView alloc] initWithFrame:iconFrame];
    iconView.contentMode = UIViewContentModeScaleAspectFit;
    iconView.backgroundColor = [UIColor clearColor];
    [header addSubview:iconView];


代码很多，不过也确实做了很多事来设置你多样的试图，简单的解释：
1. 再表头设置当前状态。
2. 创建每个需要展现天气的标签。
3. 添加一个用于放置天气图标的图片试图。

给列表一个轻推事件，当你滑动时应该有个反弹。

###获取天气数据

你会发现虽然画面显示了loading，不过没有任何反应，是时候获取一些真实的天气状况啦。

从**OpenWeatherMap**提供的api获取数据。OpenWeatherMap是一个很好的免费为大家提供实时精准的天气数据。它有很多种天气接口，不过大多数是用了类似XML的过时的技术，或者有些是需要付费的，而且很贵。

根据下列步骤来获得天气数据：
1. 获取设备地址。
2. 下载json格式的天气数据
3. 匹配数据
4. 通知ui有新的数据

先创建天气模型和数据管理类。取名**WXCLient**，继承**NSObject**.

用同样的方式做下列三件事：
- **WXManager**继承**NSObject**。
- **WXCondition**继承**MTLModel**。
- **WXDailyForecast**继承**WXConditon**。

所有都做完后，可以开始下一个步骤，匹配和转换天气数据模型。

###创建天气模型

用**Mantle**来让你更简单的实现数据匹配和值转换，打开**WXCondition.h**像下面一样修改接口：

    // 1
    @interface WXCondition : MTLModel <MTLJSONSerializing>
     
    // 2
    @property (nonatomic, strong) NSDate *date;
    @property (nonatomic, strong) NSNumber *humidity;
    @property (nonatomic, strong) NSNumber *temperature;
    @property (nonatomic, strong) NSNumber *tempHigh;
    @property (nonatomic, strong) NSNumber *tempLow;
    @property (nonatomic, strong) NSString *locationName;
    @property (nonatomic, strong) NSDate *sunrise;
    @property (nonatomic, strong) NSDate *sunset;
    @property (nonatomic, strong) NSString *conditionDescription;
    @property (nonatomic, strong) NSString *condition;
    @property (nonatomic, strong) NSNumber *windBearing;
    @property (nonatomic, strong) NSNumber *windSpeed;
    @property (nonatomic, strong) NSString *icon;
     
    // 3
    - (NSString *)imageName;
     
    @end

同样的，有很多设置代码，跟着注释编号，逐条解释：
1. **MTLjsonSetializing**协议用来告诉Mantle这个对象能够把json格式的数据匹配成O_C的属性。
2. 这些是所有的天气数据属性，你会用到一些，不过最好还是全部添加上去能够让你方便的扩展。
3. 一个简单的用于匹配天气状况和图片的方法。

运行你会发现出现错误了，你还没有引入**MTLModel.h**文件。重新编译，会出现一些新的警告，忽略就行。

先实现** -imageName**方法，打开**WXCondition.m**添加下列代码：

    + (NSDictionary *)imageMap {
        // 1
        static NSDictionary *_imageMap = nil;
        if (! _imageMap) {
            // 2
            _imageMap = @{
                          @"01d" : @"weather-clear",
                          @"02d" : @"weather-few",
                          @"03d" : @"weather-few",
                          @"04d" : @"weather-broken",
                          @"09d" : @"weather-shower",
                          @"10d" : @"weather-rain",
                          @"11d" : @"weather-tstorm",
                          @"13d" : @"weather-snow",
                          @"50d" : @"weather-mist",
                          @"01n" : @"weather-moon",
                          @"02n" : @"weather-few-night",
                          @"03n" : @"weather-few-night",
                          @"04n" : @"weather-broken",
                          @"09n" : @"weather-shower",
                          @"10n" : @"weather-rain-night",
                          @"11n" : @"weather-tstorm",
                          @"13n" : @"weather-snow",
                          @"50n" : @"weather-mist",
                          };
        }
        return _imageMap;
    }
     
    // 3
    - (NSString *)imageName {
        return [WXCondition imageMap][self.icon];
    }

解释一下：
1. 创建一个静态的NSDictionary用于存放**WXConditon**
2. 匹配每一种状态。
3. 声明一个消息来获得图像名称。

仍然在这个文件中声明一个遵循**MTLJSONSerializing**协议的** +JSONKeyPathsByPropertyKey**方法用于匹配json格式的数据。

    + (NSDictionary *)JSONKeyPathsByPropertyKey {
        return @{
                 @"date": @"dt",
                 @"locationName": @"name",
                 @"humidity": @"main.humidity",
                 @"temperature": @"main.temp",
                 @"tempHigh": @"main.temp_max",
                 @"tempLow": @"main.temp_min",
                 @"sunrise": @"sys.sunrise",
                 @"sunset": @"sys.sunset",
                 @"conditionDescription": @"weather.description",
                 @"condition": @"weather.main",
                 @"icon": @"weather.icon",
                 @"windBearing": @"wind.deg",
                 @"windSpeed": @"wind.speed"
                 };
    }

在这个例子中，dictionary的key就是**WXCondition**的属性，dictionary的value就是json的值。

你会发现从JSON数据匹配到O_C属性有些冲突，比如说属性 date是NSDate类型的，而JSON的是整型的。这时就需要进行一些转换。

Mantle的一个特性能解决这个问题：**MTLValueTransformer**。这个类让你声明一个块解决怎么转换值。

Mantle转换的语法有一点点奇怪，给特殊的属性写一个转换函数，创建一个以属性名字开始以**JSONTransformer**结尾的类方法。

看比解释更直观，所以加上下列代码到**WXCondition.m**

    + (NSValueTransformer *)dateJSONTransformer {
        // 1
        return [MTLValueTransformer reversibleTransformerWithForwardBlock:^(NSString *str) {
            return [NSDate dateWithTimeIntervalSince1970:str.floatValue];
        } reverseBlock:^(NSDate *date) {
            return [NSString stringWithFormat:@"%f",[date timeIntervalSince1970]];
        }];
    }
     
    // 2
    + (NSValueTransformer *)sunriseJSONTransformer {
        return [self dateJSONTransformer];
    }
     
    + (NSValueTransformer *)sunsetJSONTransformer {
        return [self dateJSONTransformer];
    }


下面是注释：

1. 用代码快转换值返回一个**MTLValueTransformers**值
2. 你只需要知道一次怎么转换，其他可以复用。

下一个值转换有点难理解，不过是一个简单的使用OpenWeatherMap的接口然后进行值转换的结果。天气值是一个JSON格式的数组，不过你只需要关注其中的一条。

用上诉方法，添加下列代码来实现：

    + (NSValueTransformer *)conditionDescriptionJSONTransformer {
        return [MTLValueTransformer reversibleTransformerWithForwardBlock:^(NSArray *values) {
            return [values firstObject];
        } reverseBlock:^(NSString *str) {
            return @[str];
        }];
    }
     
    + (NSValueTransformer *)conditionJSONTransformer {
        return [self conditionDescriptionJSONTransformer];
    }
     
    + (NSValueTransformer *)iconJSONTransformer {
        return [self conditionDescriptionJSONTransformer];
    }

最后一个转换只是一种习俗，接口提供的风速是m/s，考虑到你的系统是英式风格，转换成里没小时。添加下列方法和宏定义到**WXConditon.m**里。

    #define MPS_TO_MPH 2.23694f
     
    + (NSValueTransformer *)windSpeedJSONTransformer {
        return [MTLValueTransformer reversibleTransformerWithForwardBlock:^(NSNumber *num) {
            return @(num.floatValue*MPS_TO_MPH);
        } reverseBlock:^(NSNumber *speed) {
            return @(speed.floatValue/MPS_TO_MPH);
        }];
    }


你需要处理的天气接口数据有一点点的小差异，注意下实时情况和每日预报的数据：

    // current
    "main": {
        "grnd_level": 1021.87,
        "humidity": 64,
        "pressure": 1021.87,
        "sea_level": 1030.6,
        "temp": 58.09,
        "temp_max": 58.09,
        "temp_min": 58.09
    }
     
    // daily forecast
    "temp": {
        "day": 58.14,
        "eve": 58.14,
        "max": 58.14,
        "min": 57.18,
        "morn": 58.14,
        "night": 57.18
    }
    
    
实时的第一个健是main最高温度是temp_max而预报的是temp和max，除了这其他都一样，所以需要改变健名来匹配正确，打开**WXDaikyForecast.m**重写**JSONKeyPathsByPropertyKey**方法：

    + (NSDictionary *)JSONKeyPathsByPropertyKey {
        // 1
        NSMutableDictionary *paths = [[super JSONKeyPathsByPropertyKey] mutableCopy];
        // 2
        paths[@"tempHigh"] = @"temp.max";
        paths[@"tempLow"] = @"temp.min";
        // 3
        return paths;
    }

这也会覆盖WXCondition的方法，下面是注释：

1. 获得**WXCondition**的复制
2. 改变最大和最小健名来匹配每日预报
3. 返回一个新的映射

编译一下确保没有错。

















     
     
     
     
     
