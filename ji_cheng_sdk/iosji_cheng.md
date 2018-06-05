## IOS集成 {#ios}

### **准备最新IOS SDK** {#ios-sdk}

点击下载[_iOS SDK_](http://assets.growingio.com/sdk/GrowingIO-iOS-SDK-2.3.1.zip)

### **导入SDK包** {#sdk}

将SDK包解压缩并拷贝到您代码的工程目录里。

![](/images/ios_jicheng/import_sdk.png)

### **在build phases中添加依赖** {#build-phases}

![](/images/ios_jicheng/tianjiayilai.png)

![](/images/ios_jicheng/tianjiayilai_2.png)

### **添加编译参数**

![](/images/ios_jicheng/tianjiabianyicanshu.png)

### **在AppDelegate.m添加集成代码** {#appdelegate-m}

```
#import "Growing.h"
...
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        ...
        [Growing startWithAccountId:@"96a848cc38c178cd"];// 改成对应项目ID
        [Growing setImp:NO];
        [Growing setTrackerHost:@"https://vds.growingio.com"]; //修改为vds域名
        [Growing setDataHost:@"https://gio.growingio.com"];  //修改为主域名
        [Growing setGtaHost:@"https://gta.growingio.com"];  //修改为gta域名
        [Growing setHybridJSSDKUrlPrefix:@"https://assets.growingio.com/sdk/hybrid/1.0"];
        [Growing setEnableLog:YES];  // 开启调试日志 可以开启日志
}
```

### **添加URL scheme** {#url-scheme}

![](/images/ios_jicheng/tianjia_scheme.png)

### **添加激活圈选代码** {#-0}

因为您代码的复杂程度以及iOS SDK的版本差异，有时候 \[Growing handleUrl:url\] 并没有被调用。请在各个平台上调试这段代码，确保当App被URL scheme唤醒之后，该函数能被调用到。

```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
    if ([Growing handleUrl:url]) // 请务必确保该函数被调用
    {
        return YES;
    }
    return NO;
    }
```

### **重要配置项** {#-1}

2.2.8.1采集广告Banner数据

很多应用上方都有横向滚动的Banner广告。对于这样的广告，如果要收集数据，请在响应点击的控件上添加如下代码

```
UIView *view;
…
view.growingAttributesValue = 广告的唯一ID;
```

其中view是您的广告元素，请确保两点：

* 对不同广告图，广告的唯一ID也不相同
* 响应点击的控件，与设置ID的控件是同一个

例如，当您的横向滚动广告共有3张广告图时，您可以在3个响应点击的View上分别设置不同的广告唯一ID，类似如下效果：

```
view1.growingAttributesValue = @“ad1”;
view2.growingAttributesValue = @“ad2”;
view3.growingAttributesValue = @“ad3”;
```

此外，当您想采集一些可能没有文字的控件（比如UIImageView，UIView）时，也可以给属性growingAttributesValue赋值作为文字，用来在圈选的时候区分不同的内容。

