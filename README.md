# GrowingIO 私有化SDK集成手册

## 文档作用

主要用于帮助GrowingIO私部用户对web、移动端sdk代码集成

## 需要注意

本文档按照以下虚拟域名表中的域名进行集成，需要集成sdk的用户在集成前先询问自己的项目管理员了解自己的域名信息

## 虚拟域名表

| 域名 | 作用 | 端口 | 协议 |
| :--- | :--- | :--- | :--- |
| gio.growingio.com | 前端域名，打开网站的主域名 | 443 | https |
| account.growingio.com | 登陆域名，主要用户账号登陆 | 443 | https |
| gta.growingio.com | 网关域名，用于连接后端服务资源的域名 | 443 | https |
| short.growingio.com | 短链域名，短链接服务，将长链接域名转换为短链接 | 443 | https |
| vds.growingio.com | vds域名，后端数据采集的上传途径 | 443 | https |

## 更新记录

| 更新内容                                                                            | 更新人   | 日期     |
|-------------------------------------------------------------------------------------|----------|----------|
| 在“Android Cordova集成”的SDK初始化代码中增加.collectWebViewUserAgent(false)方法调用 | Zack-GIO | 20180605 |

## 

# 获取项目ID和URL Scheme {#id-url-scheme}

集成前，请向项目管理员申请登陆账号

## 添加应用

点击右上角新建—添加新应用—添加应用，选择对应的平台

![](/images/huoquxiangmuid/tianjiayingyong.png)

## 获取项目ID和URL Scheme {#id-url-scheme}

页面中标黄位置为对应的项目ID和URL Scheme（Android和IOS平台）

![](/images/huoquxiangmuid/web_ai.png)

![](/images/huoquxiangmuid/mobile_ai.png)

# 集成SDK

## 网站集成

### **代码集成** {#-0}

将以下代码放入需要网站页面统一的&lt;head&gt;&lt;/head&gt;标签中，安装成功后，除 localhost 和 IP 地址外，所有网址下的行为数据都将会被收集。

```
<script type='text/javascript'>
!function(e,t,n,g,i){e[i]=e[i]||function(){(e[i].q=e[i].q||[]).push(arguments)},n=t.createElement("script"),tag=t.getElementsByTagName("script")[0],n.async=1,n.src=('https:'==document.location.protocol?'https://':'http://')+g,tag.parentNode.insertBefore(n,tag)}(window,document,"script","assets.growingio.com/op/2.0/gio.js","gio");
  gio('init', '96a848cc38c178cd',  ##需要将此处项目ID修改为您的对应项目的ID，项目ID可以在添加应用的页面可以获得
  {'setImp':false,
//更新前为'setImp':'false', 更新不会对数据采集造成影响
   'setTrackerHost':'vds.growingio.com:443', // vds域名
   'setTrackerScheme':'https',
   'setOrigin': 'https://gio.growingio.com' // 前端主域名
  });

  //put your custom page code here
  gio('send');
</script>
```

* 注意：确保项目ID设置无误。

### **重要配置选项** {#-1}

允许页面以 iframe 形式加载：

在 GrowingIO 平台上使用可视化圈选指标功能需要使用 iframe  
来加载目标页面。如果您的网站禁止了以 iframe  
形式加载，就无法正常使用圈选功能定义指标，需要设置允许以 iframe  
形式加载。

将服务器端配置修改成X-Frame-Options: Allow-From [http://gio.growingio.com，\](http://gio.growingio.com，\)  
如果您的网站是 https ，请使用X-Frame-Options: Allow-From  
[https://gio.growingio.com](https://gio.growingio.com) 。

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

## Android集成 {#android}

### **导入SDK** {#sdk}

在 project 级别的 build.gradle 文件中添加 vds-gradle-plugin 依赖：

```
    buildscript {
            repositories {
                jcenter()
            }
            dependencies {
                classpath 'com.android.tools.build:gradle:3.0.1'
                classpath 'com.growingio.android:vds-gradle-plugin:OP-2.3.1'
//2018-05-02升级，升级前版本为2.0.7
                // NOTE: Do not place your application dependencies here; they belong
                // in the individual module build.gradle files
            }
    }
```

### **添加插件、依赖和对应资源**

在 module 级别的 build.gradle 文件中添加 com.growingio.android 插件、vds-android-agent 依赖和对应的资源：

```
apply plugin: 'com.android.application'
apply plugin: 'com.growingio.android'

android {
    compileSdkVersion 26
    buildToolsVersion "26.0.0"
    defaultConfig {
        applicationId "com.example.wenke.gioeesdkandroiddemo"
        minSdkVersion 15
        targetSdkVersion 26
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        resValue("string", "growingio_project_id", "96a848cc38c178cd")
        resValue("string", "growingio_url_scheme", "growing.34b72609d4551902")
    }//更改为对应的项目ID和URL scheme

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:26.+'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
testCompile 'junit:junit:4.12'
    compile 'com.growingio.android:vds-android-agent:OP-2.3.1@aar'

    //更新前：compile 'com.growingio.android:vds-android-agent:OP-2.0.7@aar'
}
```

* 确保URL scheme、项目ID和 SDK的版本准确。

### **添加URL scheme** {#url-scheme}

把URL Scheme添加到您的项目，以便我们唤醒您的程序，进行圈选。将该产品的URLScheme添加到你的AndroidManifest.xml中的LAUNCHER Activity下。例如

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.wenke.gioeesdkandroiddemo">

<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>


<application
    android:name=".App"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".MainActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
        <intent-filter>
            <data android:scheme="growing.a62ec2138e951a64"/>//改为对应项目的URL scheme
            <action android:name="android.intent.action.VIEW"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <category android:name="android.intent.category.BROWSABLE"/>
        </intent-filter>
    </activity>
</application>
</manifest>
```

* 请添加一整个 intent-filter 区块，并确保其中只有一个 data 字段
* 确保URL scheme准确

### **修改app.java文件** {#app-java}

在app.java中添加以下代码：

```
package com.example.wenke.gioeesdkandroiddemo;
import android.app.Application;
import com.growingio.android.sdk.collection.Configuration;
import com.growingio.android.sdk.collection.GConfig;
import com.growingio.android.sdk.collection.GrowingIO;
import com.growingio.android.sdk.utils.LogUtil;
/**
* Created by wenke on 2017/12/8.
*/
public class App extends Application {
@Override
public void onCreate() {
    super.onCreate();
    GrowingIO.startWithConfiguration(this, new Configuration()
//                .setDisableImpression(true)
            .useID()
            .trackAllFragments()
            .setTestMode(true)
            .setDebugMode(true)
            .setChannel("optest")
            .setTrackerHost("https://vds.growingio.com")  // vds域名
            .setDataHost("https://gio.growingio.com") // 前端主域名
            .setGtaHost("https://gta.growingio.com")  //gta域名
            .setHybridJSSDKUrlPrefix("https://assets.growingio.com/sdk/hybrid")
    );
    LogUtil.i("GIO", "APP ");
}
}
```

### **修改MainActivity.java文件** {#mainactivity-java}

在MainActivity.java中添加以下代码：

```
package com.example.wenke.gioeesdkandroiddemo;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button1 = (Button) findViewById(R.id.button1);
        button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Log.d("click","点击了button1");
                Toast.makeText(MainActivity.this, "click button1", Toast.LENGTH_SHORT).show();
            }
        });
    }
```

### **代码混淆** {#-0}

如果你启用了混淆，请在你的proguard-rules.pro中加入如下代码：

```
-keep class com.growingio.android.sdk.** {
    *;
}
-dontwarn com.growingio.android.sdk.**
-keepnames class * extends android.view.View
-keep class * extends android.app.Fragment {
    public void setUserVisibleHint(boolean);
    public void onHiddenChanged(boolean);
    public void onResume();
    public void onPause();
}
-keep class android.support.v4.app.Fragment {
    public void setUserVisibleHint(boolean);
    public void onHiddenChanged(boolean);
    public void onResume();
    public void onPause();
}
-keep class * extends android.support.v4.app.Fragment {
    public void setUserVisibleHint(boolean);
    public void onHiddenChanged(boolean);
    public void onResume();
    public void onPause();
}
```

### **重要配置项** {#-1}

2.3.7.1采集广告banner数据

很多应用的界面上方都有横向滚动的 Banner 广告。

对于此类广告，如果您的应用通过 ViewPager、AdapterView 或者 RecyclerView 实现，请在 Banner创建时（包括动态创建）调用下面的接口来采集数据。

GrowingIO.getInstance\(\).trackBanner\(banner, bannerDescriptions\)

其中 bannerDescriptions 是 List&lt;String&gt;类型，包含所有广告图对应的广告内容描述，内容描述需要跟广告的顺序相同。

例如，当您有 5 张广告图时，只需创建一个 String 类型的 List，然后按 5 个广告出现的顺序给 List 的元素设置对应的广告描述，同样设置 5 个元素即可。

## IOS cordova 集成 {#ios-cordova}

ios cordova集成参照ios的集成方式

## Android cordova 集成 {#android-cordova}

### **添加cordova插件** {#cordova}

```
cordova plugin add cordova-growingio-plugin
```

注意：cordova-growing-plugin依赖cordova.5.0.0以上，目前不支持低版本。

### **导入SDK** {#sdk}

ANDROID/BUILD.GRADLE文件添加

```
buildscript {
  repositories {
    jcenter()
  }
  dependencies {
    classpath 'com.android.tools.build:gradle:1.5.0'
    classpath 'com.growingio.android:vds-gradle-plugin:2.1.0' //sdk版本
  }
}
allprojects {
  repositories {
    jcenter()
  }
}
```

注意高亮部分的sdk版本

### **修改BUILD.GRADLE文件** {#build-gradle}

找到ANDROID/APP/BUILD.GRADLE文件加入以下代码

```
apply plugin: 'com.android.application'
apply plugin: 'com.growingio.android'
android {
   defaultConfig {
      resValue("string", "growingio_project_id", "96a848cc38c178cd")
      resValue("string", "growingio_url_scheme", "growing.a62ec2138e951a64")
   }//更改为对应的项目ID和URL scheme
}
dependencies {
    compile 'com.growingio.android:vds-android-agent:2.1.0@aar'
}
```

### **修改AndroidManifest.xml文件** {#androidmanifest-xml}

在AndroidManifest.xml文件添加以下代码：

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.wenke.gioeesdkandroiddemo">


<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>


<application
    android:name=".App"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".MainActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
        <intent-filter>
            <data android:scheme="growing.a62ec2138e951a64"/>
//修改为对应的URL scheme
            <action android:name="android.intent.action.VIEW"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <category android:name="android.intent.category.BROWSABLE"/>
        </intent-filter>
    </activity>
</application>
</manifest>
```

### **修改app.java文件** {#app-java}

在app.java添加以下代码

```
ackage com.example.wenke.gioeesdkandroiddemo;
import android.app.Application;
import com.growingio.android.sdk.collection.Configuration;
import com.growingio.android.sdk.collection.GConfig;
import com.growingio.android.sdk.collection.GrowingIO;
import com.growingio.android.sdk.utils.LogUtil;
/**
* Created by wenke on 2017/12/8.
*/
public class App extends Application {
@Override
public void onCreate() {
    super.onCreate();
    GrowingIO.startWithConfiguration(this, new Configuration()
//                .setDisableImpression(true)
            .useID()
            .trackAllFragments()
            .setTestMode(true)
            .setDebugMode(true)
            .setChannel("optest")
            .setTrackerHost("https://vds.growingio.com")  // vds域名
            .setDataHost("https://gio.growingio.com") // 前端主域名
            .setGtaHost("https://gta.growingio.com") // gta域名
            .setHybridJSSDKUrlPrefix("https://assets.growingio.com/sdk/hybrid")
    );
    LogUtil.i("GIO", "APP ");
}
}
```

### **配置cordova** {#cordova-0}

当应用程序打开时，需要初始化会话并启动采集功能。所以，在deviceready或者resume事件触发时，可以通过下列方式初始化会话。

```
// sample index.js
  var app = {
    initialize: function() {
      this.bindEvents();
    },
    bindEvents: function() {
      document.addEventListener('deviceready', this.onDeviceReady, false);
      document.addEventListener('resume', this.onDeviceResume, false);
    },
    onDeviceReady: function() {
      app.initGrowingIO();
    },
    onDeviceResume: function() {
      app.initGrowingIO();
    },
    initGrowingIO: function() {
      // 初始化
      try {
        var gio = window.cordova.require('cordova-plugin-growingio.GrowingIO');
        var onSucc = function(msg) {
          alert(msg);
        };
        var onFail = function(msg) {
          alert(msg);
        };
        gio.init('96a848cc38c178cd',
                {
                    channel: '员工端下载',
                    trackerHost:'https://vds.growingio.com', //vds域名
                    dataHost:'https://gio.growingio.com', // 前端主域名
                    gtaHost:'https://gta.growingio.com',  //gta域名
       hybridJSSDKUrlPrefix:'https://assets.growingio.com/sdk/hybrid'
                },
                 onSucc,
                 onFail);
      } catch(err) {
        handleException(err);
      }
    }
  };
```

## React Native SDK 集成 {#react-native-sdk}

### **说明**

react-native-growingio 用于RN开发者手动发送数据。

### **引入** {#-0}

```
npm install --save https://github.com/growingio/react-native-growingio.git
npm install
react-native link react-native-growingio
```

### **配置** {#-1}

#### **IOS** {#ios}

如果react-native link react-native-growingio失败\(成功则忽略此步骤\),即发现Libraries中没有GrowingIORNPlugin.xcodeproj,则可手动配置;

a.打开XCode's工程中, 右键点击Libraries文件夹 ➜ Add Files to &lt;...&gt;

b.去node\_modules ➜ react-native-growingio ➜ ios ➜ 选择 GrowingIORNPlugin.xcodeproj

c.在工程Build Phases ➜ Link Binary With Libraries中添加libGrowingIORNPlugin.a

添加初始化函数: 在 AppDelegate 中引入\#import "Growing.h"并添加启动方法

```
- (BOOL)application:(UIApplication *)application
  didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      ...
      // 启动GrowingIO
      [Growing startWithAccountId:@"项目ID"];

      // 其他配置
      // 开启Growing调试日志 可以开启日志
      // [Growing setEnableLog:YES];
  }
```

* 按照文档 ios集成 "在build phases中添加依赖" 进行后续配置。

#### **Android** {#android}

在Application中的onCreate方法中初始化：

```
  GrowingIO.startWithConfiguration(this, new Configuration()
    .useID()
    .trackAllFragments()
    .setChannel("**应用商店"));
```

AndroidManifest.xml以及module级别build.gradle中android defaultConfig 中添加的属性，请见官网配置。  [添加官网配置](https://docs.growingio.com/sdk-20/sdk-20-api-wen-dang/android-sdk-21-an-zhuang.html)

### **方法说明** {#-2}

| **方法名** | **参数** | **说明** |
| --- | --- | --- |
| track | \(String eventId, Object eventLevelVariable\(optional\)\) | 自定义事件（计数器类型） |
| trackWithNumber | \(String eventId, Number number, Object eventLevelVariable\(optional\)\) | 自定义事件（数值类型） |
| page | \(String page\) | 页面浏览事件 |
| setUserId | \(String userId\) | 设置登录用户ID |
| clearUserId |  | 清除登录用户ID |
| setPageVariable | \(String page, Object pageLevelVariables\) | 设置页面级变量 |
| setEvar | \(Object conversionVariables\) | 设置转化变量 |
| setPeopleVariable | \(Object peopleVariables\) | 设置用户变量 |

### JS**中调用方式** {#js}

在定义Component之前引入

```
import {
    NativeModules
} from 'react-native';
```

之后就可以使用GrowingIO的方法,例如在js中调用自定义事件方法：

```
 NativeModules.GrowingIO.track("registerSuccess", {"gender":"male"});
```

### Tips {#tips}

由于最新的ReactNative 打包gradlew存在bug，所以android在打debug包和releae包时要进行如下操作：

在工程目录下

```
mkdir Android/app/assets
```

在app build.gradle android中添加：

```
sourceSets {
     main {
         assets.srcDirs = ['assets']
     }
 }
```

在工程目录下：

```
react-native bundle --platform android --dev false --entry-file App.js --bundle-output android/app/assets/index.android.bundle  --assets-dest android/app/src/main/res/
```

demo 可见 [https://github.com/growingio/react-native-growingio/examples/App.js\#](https://github.com/growingio/react-native-growingio/examples/App.js)

# 检测数据

## 检测SDK安装状态 {#sdk}

SDK安装成功后，点击“去检测SDK安装状态”按钮

![](/images/jianceshuju/jiancesdkzhuangtai.png)

## 完成安装

选择与您的应用具有相同URL或包名的一项，点击“完成安装“

![](/images/jianceshuju/wanchenganzhuang.png)

## 查看数据

SDK安装成功后，数据会在一小时后更新，在概览页面即可进行全局指标的查看

![](/images/jianceshuju/chakanshuju.png)

![](/images/jianceshuju/chakanshuju_dashboard.png)

## 验证是否能够正常圈选

在系统中点击圈选，选择刚刚添加的应用，在地址栏输入集成SDK的系统地址，显示绿色的SDK标志，代表该页面集成SDK成功，且已经能够正常完成圈选。

![](/images/jianceshuju/yanzhengshuju_1.png)

如果显示红色标志，代表该页面成功集成SDK，按照提示内容进行问题排查，排查后仍未解决，可联系系统负责人。

![](/images/jianceshuju/yanzhengshuju_2.png)

# 上传登陆用户ID {#id}

为了在GrowingIO 后台看到登陆用户的相关趋势和指标，需要上传用户的登陆ID。

注意：如果您的应用是互联网应用，且已对接用户中心，上传的用户ID必须是用户中心统一的登录ID，如果需要上传自己应用的用户ID及其他业务数据，可以按照需求进行对应变量的上传。（根据业务需求进行埋点可与系统负责人联系）

## 后台配置

1.在GrowingIO 后台导航栏中找到“打点管理”功能

![](/images/uploaduser/dadianguanli.png)

2.点击“用户变量”，开启“登陆用户ID”

![](/images/uploaduser/useridon.png)

## 代码实施

当用户登录之后调用setUserId API，设置登录用户ID

当用户登出之后调用clearUserId，清除已经设置的登录用户ID。

参数（注意大小写）：

| **参数名称** | **参数类型** | **是否必须** | **说明** |
| --- | --- | --- | --- |
| userId | String | 是 | 用户的登录用户ID |

### **Web端** {#web}

```
//setUserId API原型
gio('setUserId', userId);

//setuserId API调用示例
gio('setUserId', '1234567890');

//clearUserId API原型和调用示例
gio('clearUserId');
```

### **Android端** {#android}

```
// setUserId API原型
GrowingIO.getInstance().setUserId(String userId);

// setuserId API调用示例
GrowingIO.getInstance().setUserId(String "1234567890");

// clearUserId API原型
GrowingIO.getInstance().clearUserId();

// clearUserId API调用示例
GrowingIO.getInstance().clearUserId();
```

### **IOS 端** {#ios}

```
// setUserId API原型
+ (void)setUserId:(NSString *)userId;

// setuserId API调用示例
[Growing setUserId:@"1234567890"];


// clearUserId API原型
+ (void)clearUserId;

// clearUserId API调用示例
[Growing clearUserId];
```

## 在图表中验证数据有效性

1.在“分析”模块中，选择新建事件分析。

![](/images/uploaduser/shijianfenxi.png)

2.在“指标”下拉菜单选择指标（建议使用全局指标如页面访问量），在“维度”下拉菜单选择登陆用户ID，在右侧表格中看到上传的登陆用户ID，代表上传成功。

![](/images/uploaduser/useridsucess.png)

