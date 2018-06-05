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
            .collectWebViewUserAgent(false) // 解决cordova的crossWebView跟系统WebView‘的兼容性问题 
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
