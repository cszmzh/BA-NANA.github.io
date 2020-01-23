---
layout: post
title: 'SpringMVC入门'
subtitle: '后端必学框架'
date: 2019-09-25
categories: 技术
tags: 后端 Spring家族
cover: 'https://ba-nana.github.io/assets/img/background-picture/SpringMVC入门.png'
comments: true
toc: true
---



## 一.概述

* 什么是MVC设计模式
  * Model : 模型数据，业务逻辑
  * View : 呈现模型，与用户进行交互
  * Controller : 负责接收并处理请求，响应客户端

![图1-MVC设计模式](../../../assets/img/图1-MVC设计模式.png)



## 二.SpringMVC详解

* 核心组件

  1.DispatcherServlet：前置控制器

  2.Handler：处理器，完成具体业务逻辑

  3.HandlerMapping：将请求映射到Handler

  4.HandlerInterceptor：处理器拦截器

  5.HandlerExecutionChain：处理器执行链

  6.HandlerAdapter：处理器适配器

  7.ModelAndView：装载模型数据和视图信息

  8.ViewResolver：视图解析器

  

* 实现流程

  1.客户端请求被DispatcherServlet接收

  2.DispatcherServlet将请求映射到Handler

  3.生成Handler以及HandlerInterceptor

  4.返回HandlerExecutionChain(Handler+HandlerInterceptor)

  5.DispatcherServlet通过HandlerAdapter执行Handler

  6.返回一个ModelAndView

  7.DispatcherServlet通过ViewResolver进行解析

  8.返回填充了模型数据的View，响应给客户端




## 三.SpringMVC的使用

### 1.基于xml配置

* 导入jar包

  ~~~ xml
  <!--导入springMvc需要的jar-->
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.3.1.RELEASE</version>
       </dependency>
  ~~~

* web.xml中配置前置控制器

  ~~~ xml
  <servlet>
      <servlet-name>SpringMVC</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <!--配置springmvc.xml的路径-->
      <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
      </init-param>
    </servlet>
    <servlet-mapping>
      <servlet-name>SpringMVC</servlet-name>
      <url-pattern>/</url-pattern>
    </servlet-mapping>
  ~~~

* MyHandler类

  ~~~ java
  public class MyHandler implements Controller {
      @Override
      public ModelAndView handleRequest(javax.servlet.http.HttpServletRequest httpServletRequest, javax.servlet.http.HttpServletResponse httpServletResponse) throws Exception {
          //装载模型数据和逻辑视图
          ModelAndView modelAndView = new ModelAndView();
          //添加模型数据
          modelAndView.addObject("name","Tom");
          //添加逻辑视图
          modelAndView.setViewName("show");
          return modelAndView;
      }
  }
  ~~~

  


* XML配置Controller，HandlerMapping组件映射

* XML配置ViewResolver组件映射

  ~~~ xml
  <!--在resources下springmvc.xml中的配置-->
  <!--配置HandlerMapping,将url请求映射到Handler-->
      <bean id="handlerMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
          <!--配置Mapping-->
          <property name="mappings">
              <props>
                  <!--配置test请求对应handler-->
                  <prop key="/test">testHandler</prop>
              </props>
          </property>
      </bean>

      <!--配置Handler-->
      <bean id="testHandler" class="com.imooc.handler.MyHandler"></bean>

      <!--配置视图解析器-->
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <!--配置前缀-->
          <property name="prefix" value="/"></property>
          <!--配置后缀-->
          <property name="suffix" value=".jsp"></property>
      </bean>
  ~~~

  

### 2.基于注解配置

* SpringMVC基础配置

* Controller，HandlerMapping通过注解进行映射

  ~~~ java
  @Controller
  public class AnnotationHandler {

      /*
      业务方法：ModelAndView完成数据传递，视图解析
       */
      @RequestMapping("/modelAndViewTest")
      public ModelAndView modelAndViewTest(){
          //创建ModelAndView
          ModelAndView modelAndView = new ModelAndView();
          //填充数据模型
          modelAndView.addObject("name","zhang");
          //设置逻辑视图
          modelAndView.setViewName("show");
          return modelAndView;
      }

      /*
      业务方法：Model传值，String进行视图解析
       */
      @RequestMapping("/ModelTest")
      public String ModelTest(Model model){
          //填充模型数据
          model.addAttribute("name","jerry");
          //设置逻辑视图
          return "show";
      }

      /*
      业务方法：Map传值，String进行视图解析
       */
      @RequestMapping("/MapTest")
      public String MapTest(Map<String,String> map){
          //填充模型数据
          map.put("name","Cat");
          //设置逻辑视图
          return "show";
      }
  }
  ~~~

  

* XML配置ViewResolver组件映射




## 四.SpringMVC数据绑定

* 将HTTP请求中的参数绑定到Handler业务方法的形参
* 用于解决req取值类型转换、封装等问题


* 常用数据绑定类型
  * 基本数据类型
  * 包装类
  * 数组
  * 对象
  * 集合（List，Set，Map）
  * JSON


controller类：

~~~ java
@Controller
public class DataBindController {

    @Autowired
    private CourseDAO courseDAO;


    @RequestMapping(value = "/baseType")
    @ResponseBody   //直接将业务方法返回值响应给客户端
    public String baseType(@RequestParam(value="id") int id){
        return "id:" + id;
    }

    @RequestMapping(value = "/packageType")
    @ResponseBody
    public String packageType(@RequestParam(value = "id") Integer id){
        return "id:"+id;
    }

    @RequestMapping(value = "/arrayType")
    @ResponseBody
    public String arrayType(String[] name){
        StringBuffer sbf = new StringBuffer();
        for(String item:name){
            sbf.append(item).append(" ");
        }
        return sbf.toString();
    }

    @RequestMapping(value = "/pojoType")
    public ModelAndView pojoType(Course course){
        courseDAO.add(course);
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("courses",courseDAO.getAll());
        return  modelAndView;
    }

    @RequestMapping(value = "/listType")
    public ModelAndView listType(CourseList courseList){
        for(Course course:courseList.getCourses()){
            courseDAO.add(course);
        }
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("courses",courseDAO.getAll());
        return modelAndView;
    }

    @RequestMapping(value = "/mapType")
    public ModelAndView mapType(CourseMap courseMap){
        for(String key:courseMap.getCourses().keySet()){
            Course course = courseMap.getCourses().get(key);
            courseDAO.add(course);
        }
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("courses",courseDAO.getAll());
        return modelAndView;
    }

    @RequestMapping(value = "/setType")
    public ModelAndView setType(CourseSet courseSet){
        for(Course course:courseSet.getCourses()){
            courseDAO.add(course);
        }
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("courses",courseDAO.getAll());
        return modelAndView;
    }	//注意set和map集合区别

    @RequestMapping(value = "jsonType")
    @ResponseBody
    public Course jsonType(@RequestBody Course course){
        course.setPrice(course.getPrice()+100);
        return  course;
    }
}
~~~



五.SpringMVC的RESTful风格

* Representational State Transfer（表述性状态转移）
* 特点：
  * 统一了客户端访问资源的接口
  * url更简洁，易于理解，便与拓展
  * 有利于不同系统之间的资源共享

