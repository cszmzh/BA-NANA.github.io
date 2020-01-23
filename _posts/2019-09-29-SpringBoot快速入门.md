---
layout: post
title: 'SpringBoot快速入门'
subtitle: '后端必学框架'
date: 2019-09-29
categories: 技术
tags: 后端 Spring家族
comments: true
toc: true
---



## 一.项目配置

* 注解

  * @Value
  * @Component
  * @ConfigurationProperties

* 配置文件案例

  ~~~ 
  //application.yml
  spring:
    profiles:
      active: dev
  ~~~

  ~~~ 
  //application-dev.yml
  server:
    port: 8081
    servlet:
      context-path: /luckymoney

  limit:
    minMoney: 0.1
    maxMoney: 9999

    description: 最少要发${limit.minMoney}元，最多可发${limit.maxMoney}元
  ~~~




## 二.Controller的使用

* Controller			处理http请求

* RestController              返回json（相当于@ResponseBody配合@Controller）

* RequestMapping         配置url映射（可传入数组，Crtl+P查看）

* PathVariable                 获取url中的数据

  ~~~ java
  // url:/say/100
  @GetMapping("/say/{id}")
      public String say(@PathVariable("id") Integer id){
          //return "说明："+limitConfig.getDescription();
          return "id:"+id;
      }
  ~~~

  


* RequestParam             获取请求参数的值

  ~~~ java
  // url:/say?id=100
  @GetMapping("/say")
  	//@RequestParam(value = "id", required = false, defaultValue = "0"）  非必须传值
      public String say(@RequestParam("id") Integer id){
          //return "说明："+limitConfig.getDescription();
          return "id:"+id;
      }
  ~~~



## 三.操作数据库

* 引入依赖

  ~~~ xml
  <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-data-jpa</artifactId>
          </dependency>
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
  </dependency>
  ~~~

* 配置文件（以Hibernate为例）

  ~~~ 
  spring:
    datasource:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/luckymoney
      username: root
      password:
    jpa:
      hibernate:
        ddl-auto: update
      show-sql: true
  ~~~

  

* 事务

  ~~~ java
   //事务：指数据库事务，同时创建两个红包，保证两个都创建或都不创建
   //注意：数据库要改为InnoDB引擎
      @Transactional
      public void createTwo(){
          Luckymoney luckymoney1 = new Luckymoney();
          luckymoney1.setProducer("张钊铭");
          luckymoney1.setMoney(new BigDecimal("520"));
          repository.save(luckymoney1);

          Luckymoney luckymoney2 = new Luckymoney();
          luckymoney2.setProducer("张钊铭");
          luckymoney2.setMoney(new BigDecimal("13145"));
          repository.save(luckymoney2);
      }
  ~~~


