---
layout: post
title: 'SpringBoot2.X中findOne方法变化'
subtitle: '相较于1.5.X版本，2.X中findOne方法无法直接传入id，下面是一些解决方案'
date: 2019-10-14
categories: 技术
tags: 后端 Spring家族 技巧与报错总结
comments: true
pinned: true
---



* SpringBoot在1.5.X版本中，可以使用以下方法通过id查询

  ~~~ java
  repository.findOne(id);
  ~~~

* 在SpringBoot2.X中，findOne()传入参数改为了

  `<S extends T> Optional<S> findOne(Example<S> var1);`

  

* 解决方法：

  1.降低版本（建议1.5.2.RELEASE）

  2.在2.X版本中用以下写法

  ```java
  //写法1
  repository.findById(id).get();
  //写法2
  repository.findById(id).orElse(null);
  ```



