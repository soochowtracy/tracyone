title: 工作一个月总结（再加上一点点iOS心得）
date: 2014-07-26 21:23:01
tags:
- ios
categories:
- IOS
---


转眼间大学已经毕业，总是伴着些许忧桑，其实也还好，离家很近。细数过往发现，其实我从没走出过家乡，小学、初中、高中、甚至是大学，最远的最远竟是苏州城里－－也算是进城了。

踏入社会的的滋味复杂而苦涩，总是伴随着挫折渐渐成长，总结下这一年实习和一个月工作所得所失：

###像一个成年人去思考

犹记得刚实习时，像个愣头青，习惯了口无遮拦，习惯了懒散的纪律观念，习惯了12月早上8点暖和的被窝，突然间一切都变了。

再这里，你就得把自己当一条狗，你什么都不是，没有什么资本值得你去炫耀，你需要做的就是努力再努力的工作。公司和学校最本质的区别是：**你给学校钱，而公司却给你钱！** 你已经是一个成年人了，别伤感，只是说了个事实。

<!--more-->

###勾心斗角总是免不了的

最近悟出了个道理，。。。。。。。。。。。。这就是道理！

人生本充满困难的（life is difficult），只不过有些人选择承受面对困难带来痛苦，有些人选择承受逃避困难带来的痛苦。当你深处生活的漩涡痛苦不已不能自拔的时候，你应该意识到这只不过是你选择了随波逐流人云亦云的生活带来痛苦而已，人世间没有哪条路是绝对的坦途。

###送三条博弈论经典真理
1. Don’t play a strictly dominated strategy 
2. Rational choice can lead to bad outcomes
3. Put yourself in other people’s shoes

别问我为什么没翻译成中文，英语只有6级的渣怕无人前途。

###重头戏来了！！！！
是时候来点干货了，其实我工作也蛮坎坷的，一会做Aurora，一会做HTML5，现在又搞起了ios,真是够折腾的。

####从登录界面说起

界面无非那些组件，个人感觉人机交互比较重要，必要的提示和校验总能让人眼前一亮，推荐个输入框抖动效果 

    if (self.userNameTF.text.length == 0) {
            [self lockAnimationForView:self.userNameTF];
        }
    if (self.passwordTF.text.length == 0) {
            [self lockAnimationForView:self.passwordTF];
        }

登录的过程总是伴随着校验，总不能让用户干等着吧，推荐个做的效果比较赞的指示器[MMProgressHUD](https://github.com/mutualmobile/MMProgressHUD)。再给个实例：

        BOOL autodismiss = YES;
        CGFloat red =  arc4random_uniform(255)/255.f;
        CGFloat blue = arc4random_uniform(256)/255.f;
        CGFloat green = arc4random_uniform(256)/255.f;
        CGColorRef color = CGColorRetain([UIColor colorWithRed:red green:green blue:blue alpha:1.0].CGColor);
        [[[MMProgressHUD sharedHUD] overlayView] setOverlayColor:color];
        CGColorRelease(color);
        [MMProgressHUD showWithTitle:@"Overlay" status:@"Random Color"];
        
        if (autodismiss == YES) {
            double delayInSeconds = 2.5;
            dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, delayInSeconds * NSEC_PER_SEC);
            dispatch_after(popTime, dispatch_get_main_queue(), ^(void){
                [MMProgressHUD dismissWithSuccess:@"Success!"];
            });
        }

####一些小小的tips

- 获取设备编号


    UIDevice *device = [UIDevice currentDevice];
    NSUUID *uniqueIdentifier = device.identifierForVendor;

- tableView选择风格


    (UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath
    {
        return UITableViewCellEditingStyleDelete | UITableViewCellEditingStyleInsert;
    }

- 排序：


     NSArray *sortDesc = @[[[NSSortDescriptor alloc] initWithKey:nil ascending:NO]];
     NSArray *sortSetArray = [timeset sortedArrayUsingDescriptors:sortDesc];

- 字符串比较：


    if ([weekDate compare:[record objectForKey:@"expense_date"]] == -1)

####反编译地址经典教程

    CGFloatlat=30.0;CGFloatlng=120.0;
    CLGeocoder*coder=[[CLGeocoderalloc]init];
    CLLocation*mapLocation=[[CLLocationalloc]initWithLatitude:latlongitude:lng];
    
    [coderreverseGeocodeLocation:mapLocationcompletionHandler:^(NSArray*placemarks,NSError*error){
    if(error){
    // Geocoder failed.
    }
    else{
    CLPlacemark*mapPlaceName=[placemarksobjectAtIndex:0];
    NSString*stateName=[mapPlaceNameadministrativeArea];
    NSLog(@"%@",stateName);
    }
    }];
     
    intmain(intargc,char*argv[])
    {
    @autoreleasepool{
    // 强制简体中文
    [[NSUserDefaultsstandardUserDefaults]setObject:[NSArrayarrayWithObjects:@"zh-Hans",nil]
    forKey:@"AppleLanguages"];
    [[NSUserDefaultsstandardUserDefaults]synchronize];
    returnUIApplicationMain(argc,argv,nil,NSStringFromClass([AppDelegateclass]));
    }
    }

###题外话

第一次写这么长的博客，其实写博客到没花多少时间，素材每天都再收集，不过搭建博客的过程略心酸。下一篇准备把怎么搭建这个博客攻略分享！！！力求做到每个非技术的兄弟们都能搭建起来自己的博客。




























