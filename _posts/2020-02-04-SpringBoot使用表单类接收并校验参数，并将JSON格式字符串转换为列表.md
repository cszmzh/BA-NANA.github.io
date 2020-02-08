---
layout: post
title: 'SpringBoot使用表单类接收并校验参数，并将JSON格式字符串转换为列表'
subtitle: '当前端传递多种参数到后端时，为了简化Controller层，可以使用这种方法。除此之外，本文还向大家介绍如何将前端传来的JSON格式字符串转换为我们需要的数据。'
date: 2020-02-04
categories: 技术
tags: 技巧与报错总结 Spring家族
comments: true
toc: true
---

## 一.仔细阅读业务需求

* 假设有一份API文档如下所示

  <img src="../../../assets/img/SpringBoot使用表单类接收复杂参数/p1.png" style="zoom:38%;" />

* 可以看到，从前端传递过来的参数较多，且items为json格式的字符串，需要进行特别处理



## 二.建立表单类接收参数

* 表单类 - OrderForm

  ~~~java
  @Data
  public class OrderForm {
  
      /**
       * 买家姓名
       */
      @NotEmpty(message = "姓名必填")
      private String name;
  
      /**
       * 买家手机号
       */
      @NotEmpty(message = "手机号必填")
      private String phone;
  
      /**
       * 买家地址
       */
      @NotEmpty(message = "地址必填")
      private String address;
  
      /**
       * 买家openid
       */
      @NotEmpty(message = "openid必填")
      private String openid;
  
      @NotEmpty(message = "购物车不能为空")
      private String items;
  
  }
  ~~~

* 其中@NotEmpty注解表示其接收参数*不能为空*或*长度为0* ，message为错误信息，除此之外常用的还有@NotNull @NotBlank，其区别如下所述

  ```java
  @NotEmpty //用在集合上面(不能注释枚举)
  @NotBlank //用在String上面
  @NotNull  //用在所有类型上面
  ```

* 使用@Valid注解做Controller层校验，必要时输出错误信息

  ~~~java
  //创建订单
      @PostMapping("/create")
      public ResultVO<Map<String,String>> create(@Valid OrderForm orderForm,
                                                 BindingResult bindingResult){
          if(bindingResult.hasErrors()){
              log.error("[创建订单]参数不正确,orderForm={}",orderForm);
              throw new SellException(ResultEnum.PARAM_ERROR.getCode(),
                      bindingResult.getFieldError().getDefaultMessage());
            					//使用以上方法获取错误信息
          }
  			......
      }
  ~~~



## 三.处理JSON格式字符串

* 添加依赖

  ~~~xml
  <dependency>
  		<groupId>com.google.code.gson</groupId>
  		<artifactId>gson</artifactId>
  </dependency>
  ~~~

* 根据第一部分文档需求，我们要提取items参数中的信息转为列表

  ~~~java
  //创建List
  List<OrderDetail> orderDetailList = new ArrayList<>();
         
  //将json转换为List，注意下列OrderDetail为接收items参数的实体类
  try {
  		orderDetailList = gson.fromJson(orderForm.getItems(),
               				new TypeToken<List<OrderDetail>>() {
                      }.getType()); 
  
  }catch (Exception e){
    	//方法上使用@Slf4j注解
  		log.error("[对象转换错误],json={}", orderForm.getItems());
  		throw new SellException(ResultEnum.PARAM_ERROR);
  }
  
  orderDTO.setOrderDetailList(orderDetailList);
  ~~~

* 以上就是要和大家分享的内容啦，利用表单类接收并处理复杂、繁多的接收参数，可以使得编码中的Controller层更简洁，代码更清晰。除了上文提到的校验注解，还有一些常用的SpringBoot校验注解：[点击这里](https://ba-nana.github.io/2020/02/04/SpringBoot%E4%B8%AD%E5%B8%B8%E7%94%A8%E7%9A%84%E6%A0%A1%E9%AA%8C%E6%B3%A8%E8%A7%A3.html)

------

