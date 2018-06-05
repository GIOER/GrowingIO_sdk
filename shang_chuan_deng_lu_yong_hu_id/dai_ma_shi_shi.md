## 代码实施

当用户登录之后调用setUserId API，设置登录用户ID

当用户登出之后调用clearUserId，清除已经设置的登录用户ID。

参数（注意大小写）：

| **参数名称** | **参数类型** | **是否必须** | **说明** |
| --- | --- | --- | --- |
| userId | String | 是 | 用户的登录用户ID |

### **Web端** {#web}

```
//setUserId API原型gio('setUserId', userId);//setuserId API调用示例gio('setUserId', '1234567890');//clearUserId API原型和调用示例gio('clearUserId');
```

### **Android端** {#android}

```
// setUserId API原型GrowingIO.getInstance().setUserId(String userId);// setuserId API调用示例GrowingIO.getInstance().setUserId(String "1234567890");// clearUserId API原型GrowingIO.getInstance().clearUserId();// clearUserId API调用示例GrowingIO.getInstance().clearUserId();
```
### **IOS 端** {#ios}

```
// setUserId API原型+ (void)setUserId:(NSString *)userId;// setuserId API调用示例[Growing setUserId:@"1234567890"];// clearUserId API原型+ (void)clearUserId;// clearUserId API调用示例[Growing clearUserId];
```


