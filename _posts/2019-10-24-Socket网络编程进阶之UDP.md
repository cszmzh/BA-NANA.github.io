---
layout: post
title: 'Socket网络编程进阶之UDP'
subtitle: '详细学习UDP，熟悉掌握UDP相关API操作'
date: 2019-10-24
categories: 技术
tags: 后端 Java Socket网络编程
comments: true
pinned: true
---



## 一.UDP概述

* UDP是什么
  * User Datagram Protocol，缩写为UDP
  * 一种用户数据报协议，又称用户数据报文协议
  * 是一个简单的面向数据报的传输层协议，正是规范RFC 768
  * 用户数据协议、非连接协议



* UDP为什么不可靠
  * 把应用程序发给网络层的数据发送后，不保留数据备份
  * UDP在IP数据报头部仅仅加入了复用和数据校验（字段）
  * 发送端产生数据，接收端从网络中抓取数据
  * 结构简单、无校验、速度快、易丢包、可广播



* UDP能做什么
  * DNS\TFTP\SNMP
  * 视频、音频、普通数据的传输



* UDP包最大长度
  * 16位（2字节） 存储长度信息
  * 2^16-1 = 65535个字节，自身协议占用8字节，可用65507字节



## 二.UDP核心API

* API-DatagramSocket

  * 用于接收与发送UDP的类
  * 不同于TCP，UDP没有合并到Socket API中

  | 常用API                                    | 描述             |
  | ---------------------------------------- | -------------- |
  | DatagramSocket()                         | 实例化对象，不指定端口和IP |
  | DatagramSocket(int port)                 | 创建监听固定窗口的实例    |
  | DatagramSocket(int port, InetAddress localAddr) | 创建固定端口并指定IP的实例 |
  | receive(DatagramPacket d)                | 接收             |
  | send(DatagramPacket d)                   | 发送             |
  | setSoTimeout(int timeout)                | 设置超时（毫秒）       |
  | close()                                  | 释放资源           |



* API-DatagramPacket

  * 用于处理报文，将byte数组、目标地址、目标端口等数据包装成报文或将报文拆成byte数组
  * 是UDP的发送实体和接收实体

  | 常用API                                    | 描述                                      |
  | ---------------------------------------- | --------------------------------------- |
  | DatagramPacket(byte[]buf, int offset, int length, InetAddress address, int port) | 前三个参数指定buf的使用区间，后两个参数指定目标机器地址和端口（仅用于发送） |
  | setData(byte[]buf, int offset, int length) | 设置数据（并设置有效区间）                           |
  | setData(byte[]buf)                       | 设置数据                                    |
  | getData() \ getOffset() \ getLength() \\... | 获取...                                   |
  | setAddress(InetAddress iaddr)            | 设置地址                                    |
  | setPort(int port)                        | 设置端口号                                   |



## 三.UDP单播、广播、多播

* 广播地址
  * 255.255.255.255 为受限广播地址
  * C网广播地址一般为 XXX.XXX.XXX.255（192.168.1.255）
  * D类网址为多播预留



* 广播地址的运算（举例）

  ~~~ 
  下面看一组例子
  - IP：192.168.124.7
  - 子网掩码：255.255.255.0
  - 网络地址：192.168.124.0
  - 广播地址：192.168.124.255

  网络地址由IP+子网掩码运算得到
  广播地址可划分网段4个：0~63 64~127 128~191 192~255

  思考问题：有两个主机，可以进行广播通信吗？
  主机1：192.168.124.7，子网掩码255.255.255.192
  主机1：192.168.124.100，子网掩码255.255.255.192

  显然是不能的，根据广播划分网段
  主机1广播地址：192.168.127.63
  主机2广播地址：192.168.124.127

  ~~~


 

## 四.局域网搜索案例

* UDP接收消息并回送消息功能

* UDP局域网广播发送

* UDP局域网回送消息

* UDPProvider

  ~~~ 
  public static void main(String[] args) {
          System.out.println("UDPProvider Started");

          //作为接收者，指定端口用于数据接收
          try {
              DatagramSocket ds = new DatagramSocket(20000);

              //构建接收实体
              final byte[] buf = new byte[512];
              DatagramPacket receivePack = new DatagramPacket(buf,buf.length);

              //接收
              ds.receive(receivePack);

              //打印接收到的信息与发送者的信息
              String ip = receivePack.getAddress().getHostAddress();
              int port = receivePack.getPort();
              int datalen = receivePack.getLength();
              String data = new String(receivePack.getData(),0,datalen);
              System.out.println("UDPProvider receive from ip:"+ip+"\tport"+port+"\tdata"+data);

              //根据发送者构建一份回送数据
              String responseData = "Receive data with len:" + datalen;
              byte[] responseDataBytes = responseData.getBytes();
              DatagramPacket responsePacket = new DatagramPacket(responseDataBytes,
                      responseDataBytes.length,receivePack.getAddress(),receivePack.getPort());

              ds.send(responsePacket);

              System.out.println("UDPProvider Finished!");
              ds.close();

          } catch (SocketException e) {
              e.printStackTrace();
          } catch (IOException e){
              e.printStackTrace();
          }
      }
      
  ~~~

* UDPSearcher

  ~~~ 
  public static void main(String[] args) {
          System.out.println("UDPSearcher Started");

          //作为搜索方，让系统分配端口
          try {
              DatagramSocket ds = new DatagramSocket();

              //测试发送一份数据给接收方
              String resquestData = "HelloWorld!" ;
              byte[] resquestDataBytes = resquestData.getBytes();
              DatagramPacket requestPacket = new DatagramPacket(resquestDataBytes,
                      resquestDataBytes.length);
              //本机2000端口
              requestPacket.setAddress(InetAddress.getLocalHost());
              requestPacket.setPort(20000);

              ds.send(requestPacket);

              //构建接收实体
              final byte[] buf = new byte[512];
              DatagramPacket receivePack = new DatagramPacket(buf,buf.length);

              //接收
              ds.receive(receivePack);

              //打印接收到的信息与发送者的信息
              String ip = receivePack.getAddress().getHostAddress();
              int port = receivePack.getPort();
              int datalen = receivePack.getLength();
              String data = new String(receivePack.getData(),0,datalen);
              System.out.println("UDPSearcher receive from ip:"+ip+"\tport"+port+"\tdata"+data);

              System.out.println("UDPSearcher Finished!");
              ds.close();

          } catch (SocketException e) {
              e.printStackTrace();
          } catch (IOException e){
              e.printStackTrace();
          }
      }
      
  ~~~


------

* 完善后代码如下：

  ~~~ 
   /**
   * UDP 提供者，用于提供服务
   */
  public class UDPProvider {

      public static void main(String[] args) throws IOException {
          // 生成一份唯一标示
          String sn = UUID.randomUUID().toString();
          Provider provider = new Provider(sn);
          provider.start();

          // 读取任意键盘信息后可以退出
          //noinspection ResultOfMethodCallIgnored
          System.in.read();
          provider.exit();
      }

      private static class Provider extends Thread {
          private final String sn;
          private boolean done = false;
          private DatagramSocket ds = null;

          public Provider(String sn) {
              super();
              this.sn = sn;
          }

          @Override
          public void run() {
              super.run();

              System.out.println("UDPProvider Started.");

              try {
                  // 监听20000 端口
                  ds = new DatagramSocket(20000);

                  while (!done) {

                      // 构建接收实体
                      final byte[] buf = new byte[512];
                      DatagramPacket receivePack = new DatagramPacket(buf, buf.length);

                      // 接收
                      ds.receive(receivePack);

                      // 打印接收到的信息与发送者的信息
                      // 发送者的IP地址
                      String ip = receivePack.getAddress().getHostAddress();
                      int port = receivePack.getPort();
                      int dataLen = receivePack.getLength();
                      String data = new String(receivePack.getData(), 0, dataLen);
                      System.out.println("UDPProvider receive form ip:" + ip
                              + "\tport:" + port + "\tdata:" + data);

                      // 解析端口号
                      int responsePort = MessageCreator.parsePort(data);
                      if (responsePort != -1) {
                          // 构建一份回送数据
                          String responseData = MessageCreator.buildWithSn(sn);
                          byte[] responseDataBytes = responseData.getBytes();
                          // 直接根据发送者构建一份回送信息
                          DatagramPacket responsePacket = new DatagramPacket(responseDataBytes,
                                  responseDataBytes.length,
                                  receivePack.getAddress(),
                                  responsePort);

                          ds.send(responsePacket);
                      }
                  }
              } catch (Exception ignored) {
              } finally {
                  close();
              }

              // 完成
              System.out.println("UDPProvider Finished.");
          }

          private void close() {
              if (ds != null) {
                  ds.close();
                  ds = null;
              }
          }

          /**
           * 提供结束
           */
          void exit() {
              done = true;
              close();
          }
      }
  }

  ~~~




* UDPSearcher

  ~~~ 
  /**
   * UDP 搜索者，用于搜索服务支持方
   */
  public class UDPSearcher {
      private static final int LISTEN_PORT = 30000;


      public static void main(String[] args) throws IOException, InterruptedException {
          System.out.println("UDPSearcher Started.");

          Listener listener = listen();
          sendBroadcast();

          // 读取任意键盘信息后可以退出
          //noinspection ResultOfMethodCallIgnored
          System.in.read();

          List<Device> devices = listener.getDevicesAndClose();

          for (Device device : devices) {
              System.out.println("Device:" + device.toString());
          }

          // 完成
          System.out.println("UDPSearcher Finished.");
      }

      private static Listener listen() throws InterruptedException {
          System.out.println("UDPSearcher start listen.");
          CountDownLatch countDownLatch = new CountDownLatch(1);
          Listener listener = new Listener(LISTEN_PORT, countDownLatch);
          listener.start();

          countDownLatch.await();
          return listener;
      }

      private static void sendBroadcast() throws IOException {
          System.out.println("UDPSearcher sendBroadcast started.");

          // 作为搜索方，让系统自动分配端口
          DatagramSocket ds = new DatagramSocket();


          // 构建一份请求数据
          String requestData = MessageCreator.buildWithPort(LISTEN_PORT);
          byte[] requestDataBytes = requestData.getBytes();
          // 直接构建packet
          DatagramPacket requestPacket = new DatagramPacket(requestDataBytes,
                  requestDataBytes.length);
          // 20000端口, 广播地址
          requestPacket.setAddress(InetAddress.getByName("255.255.255.255"));
          requestPacket.setPort(20000);

          // 发送
          ds.send(requestPacket);
          ds.close();

          // 完成
          System.out.println("UDPSearcher sendBroadcast finished.");
      }

      private static class Device {
          final int port;
          final String ip;
          final String sn;

          private Device(int port, String ip, String sn) {
              this.port = port;
              this.ip = ip;
              this.sn = sn;
          }

          @Override
          public String toString() {
              return "Device{" +
                      "port=" + port +
                      ", ip='" + ip + '\'' +
                      ", sn='" + sn + '\'' +
                      '}';
          }
      }

      private static class Listener extends Thread {
          private final int listenPort;
          private final CountDownLatch countDownLatch;
          private final List<Device> devices = new ArrayList<>();
          private boolean done = false;
          private DatagramSocket ds = null;


          public Listener(int listenPort, CountDownLatch countDownLatch) {
              super();
              this.listenPort = listenPort;
              this.countDownLatch = countDownLatch;
          }

          @Override
          public void run() {
              super.run();

              // 通知已启动
              countDownLatch.countDown();
              try {
                  // 监听回送端口
                  ds = new DatagramSocket(listenPort);


                  while (!done) {
                      // 构建接收实体
                      final byte[] buf = new byte[512];
                      DatagramPacket receivePack = new DatagramPacket(buf, buf.length);

                      // 接收
                      ds.receive(receivePack);

                      // 打印接收到的信息与发送者的信息
                      // 发送者的IP地址
                      String ip = receivePack.getAddress().getHostAddress();
                      int port = receivePack.getPort();
                      int dataLen = receivePack.getLength();
                      String data = new String(receivePack.getData(), 0, dataLen);
                      System.out.println("UDPSearcher receive form ip:" + ip
                              + "\tport:" + port + "\tdata:" + data);

                      String sn = MessageCreator.parseSn(data);
                      if (sn != null) {
                          Device device = new Device(port, ip, sn);
                          devices.add(device);
                      }
                  }
              } catch (Exception ignored) {

              } finally {
                  close();
              }
              System.out.println("UDPSearcher listener finished.");

          }

          private void close() {
              if (ds != null) {
                  ds.close();
                  ds = null;
              }
          }

          List<Device> getDevicesAndClose() {
              done = true;
              close();
              return devices;
          }
      }
  }
  ~~~

  ​

------

下一篇文章推荐：Socket网络编程进阶之TCP

