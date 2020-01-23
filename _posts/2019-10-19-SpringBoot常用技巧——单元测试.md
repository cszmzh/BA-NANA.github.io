---
layout: post
title: '单元测试——基于SpringBoot'
subtitle: '开发应用时，我们需要对程序正确性进行测试，下面了解下单元测试的基本方法和流程吧！'
date: 2019-10-19
categories: 技术
tags: 后端 Spring家族 技巧与报错总结
comments: true
pinned: true
---



## 什么是单元测试？

* 单元测试是在软件开发过程中要进行的最低级别的测试活动，软件的独立单元将在与程序的其他部分相隔离的情况下进行测试



## 基于SpringBoot中的单元测试

* 以IDEA编译器为例，生成单元测试类方法：快捷键（Crtl+shift+T）,或者光标移至要测试的类，右键->Go To->Test

* 测试类上放添加@Runwith(SpringRunner.class) 与 @SpringBootTest注解

* 测试中常用方法

  | junit中常用测试方法                             | 描述                              |
  | ---------------------------------------- | ------------------------------- |
  | assertEquals(Object expected,Object actual); | 判断是否**相等**（期待值，实际值），可以指定输出错误信息  |
  | assertNotEquals(Object expected,Object actual); | 判断是否**不相等**（期待值，实际值），可以指定输出错误信息 |
  | assertNotNull / Null(Object obj);        | 判读一个对象是否非空 (非空)                 |

  

* 下面是单元测试案例

  ~~~ java
  @RunWith(SpringRunner.class)
  @SpringBootTest
  public class CategoryServiceImplTest {

      @Autowired
      private CategoryServiceImpl categoryService;

      @Test
      //测试查找一个
      public void findOne() {
          ProductCategory productCategory = categoryService.findOne(1);
          Assert.assertEquals(new Integer(1),productCategory.getCategoryId());
      }

      @Test
      //测试查找全部
      public void findAll() {
          List<ProductCategory> productCategoryList = categoryService.findAll();
          Assert.assertNotEquals(0,productCategoryList.size());
      }

      @Test
      //测试查找类目
      public void findByCategoryTypeIn() {
          List<ProductCategory> productCategoryList = categoryService.findByCategoryTypeIn(Arrays.asList(1, 2, 3, 4));
          Assert.assertNotEquals(0,productCategoryList.size());
      }

      @Test
      //测试保存
      public void save() {
          ProductCategory productCategory = new ProductCategory("男生最爱", 8);
          ProductCategory result = categoryService.save(productCategory);
          Assert.assertNotNull(result);
      }
  }

  ~~~



* 总结：熟练地使用单元测试，可以更好帮助我们快速、准确的开发程序中某个模块的功能，而不是全部写好后运行时再慢慢debug。编写一个模块后立即进行单元测试，这对于后端开发人员来说是一个好习惯。

