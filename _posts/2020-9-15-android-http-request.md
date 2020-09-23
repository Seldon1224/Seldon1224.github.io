---
title: Android学习-网络请求
categories: [Java, Android学习]
tags: [Android学习]
---

# Android开发-发送post以及get请求

##  一. 网络请求

### 两种方式

* HttpURLConnection

  代码示例：（post）

  ```java
  public void SendRequest() {
      new Thread(new Runnable() {
          @Override
          public void run() {
              //para为post要提交的数据
              String para = "user_name=" + userName + "&user_password=" + passWord;
              HttpURLConnection connection = null;
              BufferedReader reader = null;
              try {
                  URL url = new URL("******");
                  connection = (HttpURLConnection)url.openConnection();
                  connection.setRequestMethod("POST");
                  DataOutputStream out = new DataOutputStream(connection.getOutputStream());
                  out.writeBytes(para);
                  InputStream in = connection.getInputStream();
                  //对输入流（服务器返回结果）进行获取
                  reader = new BufferedReader(new InputStreamReader(in));
                  StringBuilder response = new StringBuilder();
                  String line;
                  while((line = reader.readLine())!= null){
                      response.append(line);
                  }
                  //处理response
                  System.out.println(response);
  
              }catch (Exception e){
                  e.printStackTrace();
              }finally {
                  if(reader!=null){
                      try{
                          reader.close();
                      }catch (IOException e){
                          e.printStackTrace();
                      }
                  }
                  if(connection!=null){
                      connection.disconnect();
                  }
              }
          }
      }).start();
  }
  ```

  > android4.0以后不能在主线程发起网络请求，应该在主线程中开启一个新线程去发起网络请求

* OKHttp

## 二. 自己遇到的问题

### 1. 权限问题

​	在AndroidManifest.xml文件下加入：

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

> Tips:（一个小坑）
>
> 在Android Stduio提供的模拟器中，只有第一次安装才会生效AndroidManifest.xml文件里的权限设置，第一次安装后再增加权限代码不会生效，应该卸载再重新安装。

### 2. Cleartext HTTP traffic to xxx not permitted解决方法

> 为保证用户数据和设备的安全，Google针对下一代 Android 系统(Android P) 的应用程序，将要求默认使用加密连接，这意味着 Android P 将禁止 App 使用所有未加密的连接，因此运行 Android P 系统的安卓设备无论是接收或者发送流量，未来都不能明码传输，需要使用下一代(Transport Layer Security)传输层安全协议

因此在Android 高版本中使用HttpUrlConnection进行http请求会出现以下异常:

[![wyBILd.png](https://s1.ax1x.com/2020/09/15/wyBILd.png)](https://imgchr.com/i/wyBILd)

**解决办法：**

* 改用https请求

* 在build.grade(:app)里将targetSdkVersion改为27以下

* 在AndroidManifest.xml配置文件的<application>标签中直接插入（简单粗暴）

  ```xml
  	android:usesCleartextTraffic="true"
  ```

* 更改网络安全配置

  1. 在res文件夹下创建一个xml文件夹，然后创建一个network_security_config.xml文件

     内容如下：

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
     <network-security-config>
         <base-config cleartextTrafficPermitted="true" />
     </network-security-config>
     ```

  2. 在AndroidManifest.xml文件下的application标签增加以下属性

     ```xml
     <application
     ...
      android:networkSecurityConfig="@xml/network_security_config"
     ...
         />
     ```

>  注意：若要请求的url为localhost，则要用10.0.2.2替代