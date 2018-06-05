## React Native SDK 集成 {#react-native-sdk}

### **说明**

react-native-growingio 用于RN开发者手动发送数据。

### **引入** {#-0}

```
npm install --save https://github.com/growingio/react-native-growingio.gitnpm installreact-native link react-native-growingio
```


### **配置** {#-1}

#### **IOS** {#ios}

如果react-native link react-native-growingio失败(成功则忽略此步骤),即发现Libraries中没有GrowingIORNPlugin.xcodeproj,则可手动配置;

a.打开XCode&#039;s工程中, 右键点击Libraries文件夹 ➜ Add Files to &lt;...&gt;

b.去node_modules ➜ react-native-growingio ➜ ios ➜ 选择 GrowingIORNPlugin.xcodeproj

c.在工程Build Phases ➜ Link Binary With Libraries中添加libGrowingIORNPlugin.a

添加初始化函数: 在 AppDelegate 中引入#import &quot;Growing.h&quot;并添加启动方法


```
- (BOOL)application:(UIApplication *)application  didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {      ...      // 启动GrowingIO      [Growing startWithAccountId:@"项目ID"];      // 其他配置      // 开启Growing调试日志 可以开启日志      // [Growing setEnableLog:YES];  }
```
-   按照文档 ios集成 "在build phases中添加依赖" 进行后续配置。


#### **Android** {#android}

在Application中的onCreate方法中初始化：

```
  GrowingIO.startWithConfiguration(this, new Configuration()	.useID()	.trackAllFragments()	.setChannel("**应用商店"));
```


AndroidManifest.xml以及module级别build.gradle中android defaultConfig 中添加的属性，请见官网配置。  [添加官网配置](https://docs.growingio.com/sdk-20/sdk-20-api-wen-dang/android-sdk-21-an-zhuang.html)

### **方法说明** {#-2}

| **方法名** | **参数** | **说明** |
| --- | --- | --- |
| track | (String eventId, Object eventLevelVariable(optional)) | 自定义事件（计数器类型） |
| trackWithNumber | (String eventId, Number number, Object eventLevelVariable(optional)) | 自定义事件（数值类型） |
| page | (String page) | 页面浏览事件 |
| setUserId | (String userId) | 设置登录用户ID |
| clearUserId |  | 清除登录用户ID |
| setPageVariable | (String page, Object pageLevelVariables) | 设置页面级变量 |
| setEvar | (Object conversionVariables) | 设置转化变量 |
| setPeopleVariable | (Object peopleVariables) | 设置用户变量 |

### JS**中调用方式** {#js}

在定义Component之前引入

```
import {    NativeModules} from 'react-native';
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
sourceSets {     main {         assets.srcDirs = ['assets']     } }
```

在工程目录下：


```
react-native bundle --platform android --dev false --entry-file App.js --bundle-output android/app/assets/index.android.bundle  --assets-dest android/app/src/main/res/
```

demo 可见 https://github.com/growingio/react-native-growingio/examples/App.js