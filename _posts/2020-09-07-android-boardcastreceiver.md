---
title: Android学习-BroadcastReceiver详解及应用
categories: [Java, Android学习]
tags: [Android学习]
---

# **BroadcastReceiver** 详解及应用

Android四大组件之一：BroadcastReceiver，翻译是广播接收者。

在Android中的广播机制与现实中一样，发送广播的是Broadcast，接收广播的BroadcastReceiver及广播之间传递数据的Intent。

## 1. 注册BroadcastReceiver接收广播

* 继承BroadcastReceiver，这是一个抽象类 `public abstract class BroadcastReceiver`

  **实现抽象方法**

```java
public abstract void onReceive(Context context, Intent intent); 
```

- 当收到注册的广播时，onReceive方法会被调用。
- context是上下文，Intent就是广播携带的数据。

### 注册方法

- 静态注册（在AndroidManifest.xml清单文件中注册）

  ```xml
  <uses-permission android:name="android.permission.PROCESS_OUTGOING_CALLS"/>      
  	<receiver  android:name=".MyBroadcastReceiver">         
          <intent-filter>             
              <action  android:name="android.intent.action.NEW_OUTGOING_CALL" />           
          </intent-filter>     
  	</receiver>  
  ```

- 动态注册（在代码中注册）

>  当我们不需要一直接收某种广播时，可以使用动态注册广播接收者的方式。

 ```java
public class MainActivity extends Activity  {           
    private  MyBroadcastReceiver receiver ;            
    @Override           
    protected  void onCreate(Bundle savedInstanceState) {            
        super.onCreate(savedInstanceState);              
        setContentView(R.layout.activity_main);            
        registerMyReceiver();//在activity创建的时候进行注册监听           
    }           
    private void  registerMyReceiver() {                   
        receiver = new MyBroadcastReceiver();               
        IntentFilter  filter = new IntentFilter();//创建IntentFilter对象                   		 
        filter.addAction(Intent.ACTION_SCREEN_OFF);//IntentFilter对象中添加要接收的关屏广播
        filter.addAction(Intent.ACTION_SCREEN_ON);//添加点亮屏幕广播                   
        registerReceiver(receiver,  filter);            
    }        
    private void  unRegisterMyReceiver(){               
        if(receiver !=  null){                         
            unregisterReceiver(receiver);//反注册广播，也就是注销广播接收者，使其不起作用                  
        }             
    }  
}  
 ```



 

 

 

 

 

 

 

 

 

 

 

 

 