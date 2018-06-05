## Android集成 {#android}

### **导入SDK** {#sdk}

在 project 级别的 build.gradle 文件中添加 vds-gradle-plugin 依赖：

```
    buildscript {            repositories {                jcenter()            }            dependencies {                classpath 'com.android.tools.build:gradle:3.0.1'                classpath 'com.growingio.android:vds-gradle-plugin:OP-2.3.1'//2018-05-02升级，升级前版本为2.0.7                // NOTE: Do not place your application dependencies here; they belong                // in the individual module build.gradle files            }    }
```

### **添加插件、依赖和对应资源**

在 module 级别的 build.gradle 文件中添加 com.growingio.android 插件、vds-android-agent 依赖和对应的资源：


```
apply plugin: 'com.android.application'apply plugin: 'com.growingio.android'android {    compileSdkVersion 26    buildToolsVersion "26.0.0"    defaultConfig {        applicationId "com.example.wenke.gioeesdkandroiddemo"        minSdkVersion 15        targetSdkVersion 26        versionCode 1        versionName "1.0"        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"        resValue("string", "growingio_project_id", "96a848cc38c178cd")        resValue("string", "growingio_url_scheme", "growing.34b72609d4551902")    }//更改为对应的项目ID和URL scheme    buildTypes {        release {            minifyEnabled false            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'        }    }}dependencies {    compile fileTree(dir: 'libs', include: ['*.jar'])    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {        exclude group: 'com.android.support', module: 'support-annotations'    })    compile 'com.android.support:appcompat-v7:26.+'    compile 'com.android.support.constraint:constraint-layout:1.0.2'testCompile 'junit:junit:4.12'	compile 'com.growingio.android:vds-android-agent:OP-2.3.1@aar'
	
	//更新前：compile 'com.growingio.android:vds-android-agent:OP-2.0.7@aar'}
```

*   确保URL scheme、项目ID和 SDK的版本准确。

### **添加URL scheme** {#url-scheme}

把URL Scheme添加到您的项目，以便我们唤醒您的程序，进行圈选。将该产品的URLScheme添加到你的AndroidManifest.xml中的LAUNCHER Activity下。例如

```
<?xml version="1.0" encoding="utf-8"?><manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.wenke.gioeesdkandroiddemo"><uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/><uses-permission android:name="android.permission.INTERNET"/><uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/><application    android:name=".App"    android:allowBackup="true"    android:icon="@mipmap/ic_launcher"    android:label="@string/app_name"    android:roundIcon="@mipmap/ic_launcher_round"    android:supportsRtl="true"    android:theme="@style/AppTheme">    <activity android:name=".MainActivity">        <intent-filter>            <action android:name="android.intent.action.MAIN" />            <category android:name="android.intent.category.LAUNCHER" />        </intent-filter>        <intent-filter>            <data android:scheme="growing.a62ec2138e951a64"/>//改为对应项目的URL scheme            <action android:name="android.intent.action.VIEW"/>            <category android:name="android.intent.category.DEFAULT"/>            <category android:name="android.intent.category.BROWSABLE"/>        </intent-filter>    </activity></application></manifest>
```

*   请添加一整个 intent-filter 区块，并确保其中只有一个 data 字段
*   确保URL scheme准确

### **修改app.java文件** {#app-java}

在app.java中添加以下代码：

```
package com.example.wenke.gioeesdkandroiddemo;import android.app.Application;import com.growingio.android.sdk.collection.Configuration;import com.growingio.android.sdk.collection.GConfig;import com.growingio.android.sdk.collection.GrowingIO;import com.growingio.android.sdk.utils.LogUtil;/*** Created by wenke on 2017/12/8.*/public class App extends Application {@Overridepublic void onCreate() {    super.onCreate();    GrowingIO.startWithConfiguration(this, new Configuration()//                .setDisableImpression(true)            .useID()            .trackAllFragments()            .setTestMode(true)            .setDebugMode(true)            .setChannel("optest")            .setTrackerHost("https://vds.growingio.com")  // vds域名            .setDataHost("https://gio.growingio.com") // 前端主域名            .setGtaHost("https://gta.growingio.com")  //gta域名            .setHybridJSSDKUrlPrefix("https://assets.growingio.com/sdk/hybrid")    );    LogUtil.i("GIO", "APP ");}}
```

### **修改MainActivity.java文件** {#mainactivity-java}

在MainActivity.java中添加以下代码：

```
package com.example.wenke.gioeesdkandroiddemo;import android.support.v7.app.AppCompatActivity;import android.os.Bundle;import android.util.Log;import android.view.View;import android.widget.Button;import android.widget.Toast;public class MainActivity extends AppCompatActivity {    @Override    protected void onCreate(Bundle savedInstanceState) {        super.onCreate(savedInstanceState);        setContentView(R.layout.activity_main);        Button button1 = (Button) findViewById(R.id.button1);        button1.setOnClickListener(new View.OnClickListener() {            @Override            public void onClick(View view) {                Log.d("click","点击了button1");                Toast.makeText(MainActivity.this, "click button1", Toast.LENGTH_SHORT).show();            }        });    }
```
### **代码混淆** {#-0}

如果你启用了混淆，请在你的proguard-rules.pro中加入如下代码：

```
-keep class com.growingio.android.sdk.** {    *;}-dontwarn com.growingio.android.sdk.**-keepnames class * extends android.view.View-keep class * extends android.app.Fragment {    public void setUserVisibleHint(boolean);    public void onHiddenChanged(boolean);    public void onResume();    public void onPause();}-keep class android.support.v4.app.Fragment {    public void setUserVisibleHint(boolean);    public void onHiddenChanged(boolean);    public void onResume();    public void onPause();}-keep class * extends android.support.v4.app.Fragment {    public void setUserVisibleHint(boolean);    public void onHiddenChanged(boolean);    public void onResume();    public void onPause();}
```

### **重要配置项** {#-1}

2.3.7.1采集广告banner数据

很多应用的界面上方都有横向滚动的 Banner 广告。

对于此类广告，如果您的应用通过 ViewPager、AdapterView 或者 RecyclerView 实现，请在 Banner创建时（包括动态创建）调用下面的接口来采集数据。

GrowingIO.getInstance().trackBanner(banner, bannerDescriptions)

其中 bannerDescriptions 是 List&lt;String&gt;类型，包含所有广告图对应的广告内容描述，内容描述需要跟广告的顺序相同。

例如，当您有 5 张广告图时，只需创建一个 String 类型的 List，然后按 5 个广告出现的顺序给 List 的元素设置对应的广告描述，同样设置 5 个元素即可。