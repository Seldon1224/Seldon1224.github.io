---
title: Android学习-WebView控件的使用
categories: [Java, Android学习]
tags: [Android学习]
---

# Android-WebView控件的使用

## 1. 基本使用

​	`WebView`是一个基于`webkit`引擎、展现`web`页面的控件。

### 1.1 作用

- 显示和渲染Web页面
- 直接使用html文件（网络上或本地assets中）作布局
- 可和JavaScript交互调用

### 1.2 基本用法

 * 加载url 

   ```java
   //方式1. 加载一个网页：
   webView.loadUrl("http://www.google.com/");
   //方式2：加载apk包中的html页面
   webView.loadUrl("file:///android_asset/test.html");
   //方式3：加载手机本地的html页面
   webView.loadUrl("content://com.android.htmlfileprovider/sdcard/test.html");
   ```

* 前进 / 后退网页

  ```java
  //是否可以后退
  Webview.canGoBack() 
  //后退网页
  Webview.goBack()
  //是否可以前进                     
  Webview.canGoForward()
  //前进网页
  Webview.goForward()
  //以当前的index为起始点前进或者后退到历史记录中指定的steps
  //如果steps为负数则为后退，正数则为前进
  Webview.goBackOrForward(intsteps)
  ```

* WebSettings类

  ```java
  //声明WebSettings子类
  WebSettings webSettings = webView.getSettings();
  
  //如果访问的页面中要与Javascript交互，则webview必须设置支持Javascript
  webSettings.setJavaScriptEnabled(true);  
  
  //支持插件
  webSettings.setPluginsEnabled(true); 
  
  //设置自适应屏幕，两者合用
  webSettings.setUseWideViewPort(true); //将图片调整到适合webview的大小 
  webSettings.setLoadWithOverviewMode(true); // 缩放至屏幕的大小
  
  //缩放操作
  webSettings.setSupportZoom(true); //支持缩放，默认为true。是下面那个的前提。
  webSettings.setBuiltInZoomControls(true); //设置内置的缩放控件。若为false，则该WebView不可缩放
  webSettings.setDisplayZoomControls(false); //隐藏原生的缩放控件
  
  //其他细节操作
  webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK); //关闭webview中缓存 
  webSettings.setAllowFileAccess(true); //设置可以访问文件 
  webSettings.setJavaScriptCanOpenWindowsAutomatically(true); //支持通过JS打开新窗口 
  webSettings.setLoadsImagesAutomatically(true); //支持自动加载图片
  webSettings.setDefaultTextEncodingName("utf-8");//设置编码格式
  ```

* WebViewClient类

  > 用于处理各种通知以及请求事件

  常见用法：

  >  避免在webview上点击网页链接时转到系统浏览器中

  ```java
  webView.setWebViewClient(new WebViewClient(){
    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        view.loadUrl(url);
    return true;
    }
  });
  ```

## 2. 错误解决 

### 2.1 页面出现“net::ERR_CACHE_MISS”错误

* 原因：APP权限不够

* 解决：在AndroidManifest.xml文件中加入：

  ```xml
  <uses-permission android:name="android.permission.INTERNET" />
  ```

  > 加权限非常重要！

  在Activity中加入：

  ```java
  WebSettings settings =  webView.getSettings();
  settings.setAllowFileAccess(true);
  settings.setAllowContentAccess(true);
  ```

### 2.2 页面加载出来后，无法实现页面JS部分的功能

​	在Activity中加入以下代码：

    settings.setJavaScriptEnabled(true);

### 2.3 按下返回键后，直接退出了整个程序

按下返回键后，系统直接调用了该Activity的finish()函数，退出了本程序，一个对按下返回的事件处理函数就行重写。

代码如下：

```java
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    if ((keyCode == KeyEvent.KEYCODE_BACK) && webView.canGoBack()) {
        webView.goBack();
        return true;
    }
    return super.onKeyDown(keyCode, event);
}
```



