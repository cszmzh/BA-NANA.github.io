---
layout: post
title: 'SpringBoot出现a bean named entityManagerFactory that could not be found'
subtitle: '在初次运行SpringBoot时出现该错误，下面是解决方法。'
date: 2019-12-11
categories: 技术
tags: 技巧与报错总结
comments: true
toc: true
---



* 具体错误

  ~~~ 
  a bean named 'entityManagerFactory' that could not be found
  ~~~

* 原因：maven中依赖导入的包可能和其他jar包冲突

* 解决方法：

  ~~~ 
  <dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
  <version>RELEASE</version>
  <scope>compile</scope>
  </dependency>
  ~~~

  把上述version一栏去掉，变为

  ~~~ 
  <dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
  <scope>compile</scope>
  </dependency>
  ~~~

