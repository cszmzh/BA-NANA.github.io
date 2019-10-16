---
layout: post
title: 'SpringBoot进阶'
subtitle: '后端必学框架'
date: 2019-09-30
categories: 技术
tags: 后端 Spring家族
comments: true
toc: true
---



## 一.表单验证（@Valid）

* 实体类

  ~~~ 
  @Min(value = 18, message = "未成年少女禁止入内")
      private Integer age;
  ~~~

* 控制器类

  ~~~ 
  /**
       * 添加一个女生
       * @return
       */
      @PostMapping(value = "/girls")
      public Girl girlAdd(@Valid Girl girl, BindingResult bindingResult) {
          if(bindingResult.hasErrors()){
              System.out.println(bindingResult.getFieldError().getDefaultMessage());
              return null;
          }

          girl.setCupSize(girl.getCupSize());
          girl.setAge(girl.getAge());

          return girlRepository.save(girl);
      }
  ~~~

  ​

## 二.使用AOP处理请求

* @通知类型(value="execution(* com.banana.impl..*.\*(..))")

  | 符号              | 含义                         |
  | --------------- | -------------------------- |
  | execution()     | 表达式的主体                     |
  | 第一个" * "符号      | 返回值的类型任意                   |
  | com.banana.impl | 切点的包名                      |
  | ..*             | 当前包及子包的所有类                 |
  | .*(..)          | 表示任何方法名，括号表示参数，两个点表示任何参数类型 |

  ~~~ 
  //例子
  @Aspect
  @Component
  public class HttpAspect {
      @Before("execution(public * com.imooc.controller.GirlController.*(..))")
      public void log(){
          System.out.println("before");
      }
  }
  ~~~

* 日志处理

  ~~~ 
  @Aspect
  @Component
  public class HttpAspect {

      private  final  static Logger logger = LoggerFactory.getLogger(HttpAspect.class);

      @Pointcut("execution(public * com.imooc.controller.GirlController.*(..))")
      public void log(){

      }

      @Before("log()")
      public void doBefore(JoinPoint joinPoint){
          ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
          HttpServletRequest request = attributes.getRequest();

          //url
          logger.info("url={}",request.getRequestURL());
          //method
          logger.info("method={}",request.getMethod());
          //ip
          logger.info("ip={}",request.getRemoteAddr());
          //类方法
          logger.info("class_method={}",joinPoint.getSignature().getDeclaringType()+"."+joinPoint.getSignature().getName());
          //参数
          logger.info("args={}", joinPoint.getArgs());
      }
      
       @AfterReturning(returning = "object",pointcut = "log()")
      public void doAfterReturning(Object object){
          logger.info("response={}",object.toString());
      }
    }
  ~~~




  ## 三.异常处理

  * 统一异常处理



  ## 四.单元测试

* 测试Service

  ~~~ 
  @RunWith(SpringRunner.class)

    @SpringBootTest

    public class GirlServiceTest {

    @Autowired
    private GirlService girlService;
    
    @Test
    public void Test(){
        ...
        Assert.assertEquals(...);
    }
  }
  ~~~

* 测试API

  ~~~ 
      @RunWith(SpringRunner.class)
      
      @SpringBootTest
      
      @AutoConfigureMockMvc
      
      public class GirlControllerTest {
        
        @Autowired
        private MockMvc mvc;
        
        @Test
        public void girlList() throws Exception{
            mvc.perform(MockMvcRequestBuilders.get("/girls"))
                    .andExpect(MockMvcResultMatchers.status().isOk())
                    .andExpect(MockMvcResultMatchers.content().string("abc..."));
        }
      }
   
  ~~~

