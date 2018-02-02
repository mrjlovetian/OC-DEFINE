# iOS常用宏定义
## 颜色
```
#define PCBRGBColorA(r, g, b, a) [UIColor colorWithRed:(r)/255.0 green:(g)/255.0 blue:(b)/255.0 alpha:(a)/255.0]//RGBA
#define PCBRGBColor(r, g, b) PCBRGBColorA((r), (g), (b), 255)//RGB
#define PCBRandomColor PCBRGBColor(arc4random_uniform(255), arc4random_uniform(255), arc4random_uniform(255))//随机色
#define PCBColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]// rgb颜色转换（16进制->10进制）
#define PCBWhiteColor [UIColor whiteColor]
#define PCBBlackColor [UIColor blackColor]
#define PCBDarkGrayColor [UIColor darkGrayColor]
#define PCBLightGrayColor [UIColor lightGrayColor]
#define PCBGrayColor [UIColor grayColor]
#define PCBRedColor [UIColor redColor]
#define PCBGreenColor [UIColor greenColor]
#define PCBBlueColor [UIColor blueColor]
#define PCBCyanColor [UIColor cyanColor]
#define PCBYellowColor [UIColor yellowColor]
#define PCBMagentaColor [UIColor magentaColor]
#define PCBOrangeColor [UIColor orangeColor]
#define PCBPurpleColor [UIColor purpleColor]
#define PCBBrownColor [UIColor brownColor]
#define PCBClearColor [UIColor clearColor]

/// PCB常见颜色
#define PCB_BlackColor PCBColorFromRGB(0x333333)
#define PCB_GrayColor  PCBColorFromRGB(0x999999)
#define PCB_Gray_BgButtonColor PCBColorFromRGB(0xd1d1d1)
#define PCB_GreenColor PCBColorFromRGB(0x30c1ce)
#define PCB_BgColor  PCBColorFromRGB(0xf1f1f1)
#define PCB_LineColor  PCBColorFromRGB(0xe5e5e5)
```
## 打印
```
#ifdef DEBUG
#define PCBLog(...) NSLog(@"\n打印结果:\n %s    第%d行 \n %@\n\n",__func__,__LINE__,[NSString stringWithFormat:__VA_ARGS__])
#else
#define PCBLog(...)
#endif

/// 只打印函数名称
#define PCBLogFunc  NSLog(@"方法名:\n %s", __func__);
#define PCBLogResponseObject PCBLog(@"%@",responseObject);
#define PCBLogError PCBLog(@"%@",error);
#define PCBNSHomeDirectory PCBLog(@"%@",NSHomeDirectory());
```
## 单利
```
/// 单例化一个类
#if __has_feature(objc_arc)//ARC

#define SYNTHESIZE_SINGLETON_FOR_CLASS_HEADER(classname) \
\
+ (classname *)shared##classname;

#define SYNTHESIZE_SINGLETON_FOR_CLASS(classname) \
\
static classname *shared##classname = nil; \
\
+ (classname *)shared##classname \
{ \
static dispatch_once_t pred; \
dispatch_once(&pred, ^{ shared##classname = [[classname alloc] init]; }); \
return shared##classname; \
}

#else//MRC

#define SYNTHESIZE_SINGLETON_FOR_CLASS_HEADER(classname) \
\
+ (classname *)shared##classname;

#define SYNTHESIZE_SINGLETON_FOR_CLASS(classname) \
\
static classname *shared##classname = nil; \
\
+ (classname *)shared##classname \
{ \
static dispatch_once_t pred; \
dispatch_once(&pred, ^{ shared##classname = [[classname alloc] init]; }); \
return shared##classname; \
} \
\
- (id)copyWithZone:(NSZone *)zone \
{ \
return self; \
} \
\
- (id)retain \
{ \
return self; \
} \
\
- (NSUInteger)retainCount \
{ \
return NSUIntegerMax; \
} \
\
- (oneway void)release \
{ \
} \
\
- (id)autorelease \
{ \
return self; \
}

#endif
```
## 字体
```
/*
#define PCBFontName  @"PingFangSC-Medium"
#define PCBSystemFontWithSize(R)  [UIFont fontWithName: PCBFontName size: R]

/// 固定字体
#define PCBFont(float) PCBSystemFontWithSize(float)
/// 可以适配的字体
#define PCBAdaptFont(float) PCBSystemFontWithSize(PCBAdapted_Width(float))
*/

/// 字体大小适应
#define PCBFont(float) [UIFont systemFontOfSize:float]
#define PCBBoldFont(float) [UIFont boldSystemFontOfSize:float]
#define PCBFont_13 PCBFont(13)
#define PCBFont_14 PCBFont(14)
#define PCBFont_15 PCBFont(15)
#define PCBFont_17 PCBFont(17)
#define PCBFont_20 PCBFont(20)
```
## 尺寸 + 版本
```
/*
 iPhone X尺寸问题
 分辨率1125*2436  （375*812)
 导航栏为 44+44  原来为20+44
 底部tabbar  49+34  其余屏幕 49+0
 */
#define IOS11   @available(iOS 11.0, *)
#define IPhoneX ([UIScreen mainScreen].bounds.size.width == 375.0f && [UIScreen mainScreen].bounds.size.height == 812.0f)

/// 尺寸
#define PCBScreen_Width          ([UIScreen mainScreen].bounds.size.width)
#define PCBScreen_Height         ([UIScreen mainScreen].bounds.size.height)
#define PCBStatusBar_Height      [[UIApplication sharedApplication] statusBarFrame].size.height
#define PCBNavigationBar_Height  self.navigationController.navigationBar.frame.size.height
#define PCBHeight_64             (PCBStatusBar_Height + PCBNavigationBar_Height)
#define PCBTabBar_Height         (IPhoneX ? 83.f : 49.f)
#define PCBScreen_Bounds         [UIScreen mainScreen].bounds
#define PCBShowView_Height       (Screen_Height - PCBHeight_64 - PCBTabBar_Height)

/// 适配
/// 现在产品设计稿有以iPhone6s为基准的
#define PCBScreenWidthRatio      (PCBScreen_Width / 375.0)
#define PCBScreenHeightRatio     (PCBScreen_Width / 375.0)//(PCBScreen_Height / 667.0)-适配ihopneX以屏幕宽度比例为准
#define PCBAdapted_Width(x)      (ceilf((x) * PCBScreenWidthRatio))
#define PCBAdapted_Height(x)     (ceilf((x) * PCBScreenHeightRatio))
#define PCBMarginWidth           PCBAdapted_Width(8)
#define PCBHeight                PCBAdapted_Height(8)
#define PCBLineViewHeight        0.5

/*
 round：如果参数是小数，则求本身的四舍五入。
 ceil：如果参数是小数，则求最小的整数但不小于本身.
 floor：如果参数是小数，则求最大的整数但不大于本身.
 */
```
## 缩写
```
// 获取图片资源（本地）
#define PCBGetImage(imageName) [UIImage imageNamed:[NSString stringWithFormat:@"%@",imageName]]
#define PCBString(object) [NSString stringWithFormat:@"%@",object]

/// 系统一些常用缩写
#define PCBNotificationCenter           [NSNotificationCenter defaultCenter]
#define PCBApplication                  [UIApplication sharedApplication]
#define PCBKeyWindow                    [UIApplication sharedApplication].keyWindow
#define PCBAppDelegate                  [UIApplication sharedApplication].delegate

#define UserDefaults                    [NSUserDefaults standardUserDefaults]
#define PCBSetUserDefaults(value,key)   [UserDefaults setObject:value forKey:key];\
[UserDefaults synchronize]
#define PCBGetUserDefaults(key)         [UserDefaults objectForKey:key]
#define PCBRemoveUserDefaults(key)      [UserDefaults removeObjectForKey:key]
#define PCBNotificationCenter           [NSNotificationCenter defaultCenter]
#define PCBNavigationView               self.navigationController.view
```
## 弱引用/强引用
```
#define PCBWeakSelf(type)    __weak typeof(type) weak##type = type;
#define PCBStrongSelf(type)  __strong typeof(type) type = weak##type;
```
## 圆角、边框
```
#define PCBViewBorderRadius(View, Radius, Width, Color)\
\
[View.layer setCornerRadius:(Radius)];\
[View.layer setMasksToBounds:YES];\
[View.layer setBorderWidth:(Width)];\
[View.layer setBorderColor:[Color CGColor]]
```
## 提示框
```
/// AlertView
#define  PCBAVShow(Message)   [[[UIAlertView alloc]initWithTitle:@"提示" message:Message delegate:nil cancelButtonTitle:@"好的" otherButtonTitles:nil, nil] show]

/// UIAlertController的宏定义
/// 参数中的 MESSAGE是提示内容,  PCBVC是UIViewController就是你当前操作的页面,调用时直接传递 self
#define PCBACShow(Message,PCBVC) \
UIAlertController *alertController = [UIAlertController alertControllerWithTitle:@"温馨提示" message:Message preferredStyle:UIAlertControllerStyleAlert]; \
UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:nil]; \
[alertController addAction:okAction]; \
[PCBVC presentViewController:alertController animated:YES completion:nil];
```
## 判断字符串、数组、字典、对象为空
```
/// 字符串是否为空
#define PCBStringIsEmpty(str) ([str isKindOfClass:[NSNull class]] || str == nil || [str length] < 1 ? YES : NO )
/// 数组是否为空
#define PCBArrayIsEmpty(array) ((array == nil || [array isKindOfClass:[NSNull class]] || array.count == 0) ? YES : NO)
/// 字典是否为空
#define PCBDictIsEmpty(dic) ((dic == nil || [dic isKindOfClass:[NSNull class]] || dic.allKeys == 0) ? YES : NO)
/// 是否是空对象
#define PCBObjectIsEmpty(_object) ((_object == nil \
|| [_object isKindOfClass:[NSNull class]] \
|| ([_object respondsToSelector:@selector(length)] && [(NSData *)_object length] == 0) \
|| ([_object respondsToSelector:@selector(count)] && [(NSArray *)_object count] == 0)) ? YES : NO)
```
## 获取一些路径
```
/// 获取沙盒Document路径
#define PCBDocumentPath [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
/// 获取沙盒temp路径
#define PCBTempPath NSTemporaryDirectory()
/// 获取沙盒Cache路径
#define PCBCachePath [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) firstObject]
/// Library/Caches 文件路径
#define PCBFilePath ([[NSFileManager defaultManager] URLForDirectory:NSCachesDirectory inDomain:NSUserDomainMask appropriateForURL:nil create:YES error:nil])
#endif /* AppMacros_h */
```





