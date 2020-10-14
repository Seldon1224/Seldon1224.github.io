---
title: Android学习-Scoket编写
categories: [Java, Android学习]
tags: ['Android','Socket']
---

# Android-TCP通信socket编程

## 1. Socket

​	套接字，**是应用层 与 `TCP/IP` 协议族通信的中间软件抽象层，表现为一个封装了 TCP / IP协议族 的编程接口（API）**

 * 与UDP传输区别：

   * TCP/IP协议是一个**协议簇**。里面包括很多协议，UDP只是其中的一个。

   * TCP/IP协议集包括**应用层,传输层，网络层，网络访问层**。
   * TCP协议和UDP协议的区别：
     * TCP（Transmission Control Protocol，传输控制协议）是面向连接的协议，在收发数据前，必须和对方建立可靠的连接。此方式大大提高了数据通信的可靠性，使发送数据端和接收端在数据正式传输前就有了交互， 为数据正式传输打下了可靠的基础。
     * UDP是一个**非连接**的协议，传输数据之前源端和终端**不建立连接**， 当它想传送时就简单地去抓取来自应用程序的数据，并尽可能快地把它扔到网络上。 在发送端，UDP传送数据的速度仅仅是受应用程序生成数据的速度、 计算机的能力和传输带宽的限制； 在接收端，UDP把每个消息段放在队列中，应用程序每次从队列中读一个消息段。

* 客户端，服务器具体原理图：

​	![](https://upload-images.jianshu.io/upload_images/944365-8df0ed7afe6b32d1.png?imageMogr2/auto-orient/strip|imageView2/2/w/610/format/webp)

* Java-Socket编程

  * 作为客户端

  ​	创建一个Socket对象，指定服务器端的ip地址和端口号，申请连接。通过输入流InPutStream读取服务端的数据，通过输出流OutPutStream向服务端写数据。

  ```java
  import java.io.DataOutputStream;
  import java.io.IOException;
  import java.io.OutputStream;
  import java.net.*;
  public class TCPClient {
  
      public static void main(String[] args) throws Exception {
          Socket s = new Socket("192.168.1.100", 8000);//申请连接
          OutputStream os = s.getOutputStream();//向服务器发送数据
          DataOutputStream dos = new DataOutputStream(os);
          dos.writeUTF("hello server!");
          dos.flush();
          dos.close();
          s.close();
      }
  }
  ```

  * 作为服务器

  ​	首先声明一个`ServerSocket`对象并指定端口号，然后调用`ServerSocket`的`accept()`方法接收客户端的数据。`accept()`方法在没有客户端请求连接之前处于阻塞状态，一旦接收到连接请求，则通过输入流读取接收的数据。

  ```java
  import java.io.DataInputStream;
  import java.net.*;
  public class TCPServer {
      public static void main(String[] args) throws Exception{
              ServerSocket ss = new ServerSocket(8000); //开启8000端口
              //不止接受一个客户端
              while (true) {
              Socket s = ss.accept();//接受一个连接
              DataInputStream dis = new DataInputStream(s.getInputStream());//输入管道
              System.out.println(dis.readUTF());
              dis.close();
              s.close();
          }
      }
  
  }
  ```

## 2. Android-Socket

> Tips：
>
> * 一个Socket对象实例代表一个服务器与客户端之间的连接。
>
> * 编写程序前要添加网络权限：在manifest.xml文件中添加<uses-permission android:name="android.permission.INTERNET" />

​	在安卓里，涉及到**网络连接等耗时操作**时，不能将其放在**UI主线程**中，需要添加子线程，在子线程进行网络连接，这就涉及到安卓**线程间的通信**了，需要用**Handle**来实现。

```java
//服务器开启按钮button_connect
button_connect.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                button_connect.setEnabled(false);
                final Handler handler = new Handler(Looper.getMainLooper()){
                    @SuppressLint("SetTextI18n")
                    @Override
                    public void handleMessage(@NonNull Message msg) {
                        super.handleMessage(msg);
                        // TODO handler message
                        
                    }
                };

                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        try {
                            serverSocket = new ServerSocket(SERVER_PORT); 
                            while(true){
                                try (Socket clientSocket = serverSocket.accept()) {
                                    //成功连接
                                    Message messageConnect = new Message();
                                    messageConnect.obj = "1";
                                    handler.sendMessage(messageConnect);
                                    System.out.println("Server:Receiving...");
                                    out = new PrintStream(clientSocket.getOutputStream());
                                    in = new BufferedReader(
                                            new InputStreamReader(clientSocket.getInputStream())
                                    );
                                    
                                    ReadStr = in.readLine();
                                    System.out.println("Server:Received: '" + ReadStr + "'");
                                } catch (Exception e) {
                                    System.out.println("Server:Error");
                                    e.printStackTrace();
                                } finally {
                                    System.out.println("Server:Close.");
                                }
                            }
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                }).start();
            }
```



​	

