---
layout: post
title: 'SpringBoot解决InvalidDataAccessApiUsageException'
subtitle: '在SpringData写Repository进行单元测试时出现该错误:Executing an update/delete query，下面是网上查询到的解决办法。'
date: 2019-12-11
categories: 技术
tags: 技巧与报错总结
comments: true
toc: true
---



* 出现Executing an update/delete query

  ~~~ 
InvalidDataAccessApiUsageException: Executing an update/delete query
  ~~~

  
  
  * 原因：执行update或delete语句时没有处理事物
  
  * 方法一：使用@Transactional注解
  
  ~~~ 
    @Modifying
        @Transactional
        @Query("update Wishes set wishStatus=:wishStatus where wishId=:wishId")
        int updateWishStatus(@Param("wishId")Integer wishId, @Param("wishStatus")Integer wishStatus);
    ~~~
  
  * 方法二：使用AOP声明式事务