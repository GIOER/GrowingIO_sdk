## 网站集成

### **代码集成** {#-0}

将以下代码放入需要网站页面统一的&lt;head&gt;&lt;/head&gt;标签中，安装成功后，除 localhost 和 IP 地址外，所有网址下的行为数据都将会被收集。

```
<script type='text/javascript'>!function(e,t,n,g,i){e[i]=e[i]||function(){(e[i].q=e[i].q||[]).push(arguments)},n=t.createElement("script"),tag=t.getElementsByTagName("script")[0],n.async=1,n.src=('https:'==document.location.protocol?'https://':'http://')+g,tag.parentNode.insertBefore(n,tag)}(window,document,"script","assets.growingio.com/op/2.0/gio.js","gio");  gio('init', '96a848cc38c178cd',  ##需要将此处项目ID修改为您的对应项目的ID，项目ID可以在添加应用的页面可以获得  {'setImp':false,//更新前为'setImp':'false', 更新不会对数据采集造成影响   'setTrackerHost':'vds.growingio.com:443', // vds域名   'setTrackerScheme':'https',   'setOrigin': 'https://gio.growingio.com' // 前端主域名  });  //put your custom page code here  gio('send');</script>
```

*   注意：确保项目ID设置无误。

### **重要配置选项** {#-1}

允许页面以 iframe 形式加载：

在 GrowingIO 平台上使用可视化圈选指标功能需要使用 iframe
来加载目标页面。如果您的网站禁止了以 iframe
形式加载，就无法正常使用圈选功能定义指标，需要设置允许以 iframe
形式加载。

将服务器端配置修改成X-Frame-Options: Allow-From http://gio.growingio.com，\
如果您的网站是 https ，请使用X-Frame-Options: Allow-From
https://gio.growingio.com 。