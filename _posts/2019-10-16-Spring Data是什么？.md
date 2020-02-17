---
layout: post
title: 'Spring Data是什么？'
subtitle: '这个优秀的持久层解决方案使得我们访问数据库变得更简单，其实现方法值得我们去仔细探索'
date: 2019-10-16
categories: 技术
tags: 后端 Spring家族
cover: 'https://cdn.jsdelivr.net/gh/BA-NANA/ba-nana.github.io@1.0/assets/img/background-picture/Spring Data是什么？.png'
comments: true
toc: true
---



## 一.概述

* Spring Data作为SpringSource的其中一个子项目，旨在统一和简化对各类型持久化存储和访问，支持关系型数据库和非关系型数据库，使开发人员对数据库的访问更方便和快捷。



* Spring Data主要应用场景
  * **Spring Data JPA（重点学习）**
  * Spring Data Mongo DB
  * Spring Data Redis
  * Spring Data Solr




## 二.Spring Data JPA介绍

* 关于Repository接口

  * 是Spring Data的核心接口，不提供任何方法

  * public interface Repository \<T, ID extends Serializable> { }     //T为实体类

    ```
    1）Repository是一个空接口，标记接口
    没有包含方法

    2）若接口没有extends Repository则会报错

    ```

  * @RepositoryDefinition注解

    ~~~ java
    <!--添加注解达到extends Repository的功能-->
    @RepositoryDefinition(domainClass = 实体类文件名(不要忘记后缀.class), idClass = Integer.class)
    <!--idClass中的Integer为主键类型，按照实际情况修改-->
    ~~~



* Repository子接口

  | 接口                         | 描述                          |
  | -------------------------- | --------------------------- |
  | CrudRepository             | 继承Repository，实现CRUD相关方法     |
  | PagingAndSortingRepository | 继承CrudRepository，实现分页排序相关方法 |
  | JpaRepository              | 继承上述接口，实现 JPA规范相关方法         |



### Repository中查询方法定义规则和使用

* 了解Spring Data中查询方法名称的定义规则

* 使用Spring Data完成复杂查询方法名称命名

  ![Repository命名规范](../../../assets/img/Repository命名规范.png)

  

  * 缺点：1.方法名较长      2.对于复杂的查询难以实现



### Query注解使用

* 在Repository方法中使用，不需要遵循查询方法命名规则

* 将@Query加在在Repository中的方法上面

* 命名参数及索引参数的使用

* 本地查询

  ~~~ java
  @Query("select o from Users o where id=(select max(id) from Users tb_name)")
  public Users getUserById();	//例子：用自定义的命名规则查最大Id用户

  @Query("select o from Users o where o.name=?1 and o.age=?2")
  public List<Users> query1(String name, Integer age);	//例子：查询特定名字和年龄的用户(方法1)

  @Query("select o from Users o where o.name=:name and o.age=:age")
  public List<Users> query2(@Param("name")String name,@Param("age") Integer age);	//例子：查询特定名字和年龄的用户(方法2)

  @Query("select o from Users o where o.name like %?1%")
  public List<Users> queryLike1(String name);	//例子：模糊查询用户名（方法1）

  @Query("select o from Users o where o.name like %:name%")
  public List<Users> queryLike2(@Param("name")String name);	//例子：模糊查询用户名（方法2）

  @Query(nativeQuery = true, value="select count(*) from Users")
  public long getCount();		//例子：原生查询总数
  ~~~

  

### 更新及删除操作整合事务的使用

* 事务在Spring Data中使用

  ~~~ 
  1）事务一般在Service层
  2）@Query @Modifying @Transactional综合使用
  3）若不使用事务将无法update或delete（报错）
  ~~~

* @Modifying结合@Query注解执行更新操作

  Service层：

  ~~~ java
  @Service
  public class Service{
    
    @Autowired
    private Repository repository;
    
    @Transactional
    public void update(Integer id, Integer age){
      repository.update(id,age);
    }
  }
  ~~~

  repository:

  ~~~ java
  @Modifying
  Query("update Users o set o.age=:age where o.id=:id")
  public void update(@Param("id")Integer id, @Param("age")Integer age);
  ~~~




## 三.Spring Data JPA进阶

### CrudRepository接口的使用

* extends CrudRepository<实体类名, 主键类型>
* 提供基本增删改查操作



### PagingAndSortingRepository接口的使用

* 该接口包含分页和排序功能

* 带排序的**分页查询：findAll(Pageable pageable)**

  ~~~ java
  /**代码举例，假设数据库中有100条数据*/
  //注意：此处Pageable在org.springframework.data.domain中
  Pageable pageable = new PageRequest(0,5);
  //取第0页(传入的index值为0开始)，每页5条记录
  Page<Users> page = repository.findAll(pageable);

  System.out.println("查询的总页数：" + page.getTotalPages());
  System.out.println("查询的总记录数：" + page.getTotalElements());
  System.out.println("查询的当前为第几页：" + page.getNumber() +1 );
  System.out.println("查询的当前页面对象集合：" + page.getContent());
  System.out.println("查询的当前页面记录数：" + page.getNumberOfElements());
  ~~~

  输出结果：

  ~~~ 
  查询的总页数：20	//解析：100条数据分每页5条，一共20页
  查询的总记录数：100
  查询的当前为第几页：1		//解析：与传入的page值有关
  //此处输出语句为该页对象的集合
  查询的当前页面记录数：5

  ~~~

* **排序查询：findAll(Sort sort)**

  ~~~java
  /**代码举例，假设数据库中有100条数据*/
  Sort.Order order1 = new Sort.Order(Sort.Direction.DESC, "id"); 	//Id降序排列
  Sort.Order order2 = new Sort.Order(Sort.Direction.ASC, "id"); 	//Id升序排列
  Sort sort = new Sort(order1);	//传入Order，关系型数据
  Pageable pageable = new PageRequest(0,5,sort);
  //Pageable为一个接口，PageRequest是其一个实现类

  ~~~

  

### JpaRepository接口的使用

|          常用方法           |
| :---------------------: |
|         findAll         |
|   findAll(Sort sort)    |
|     save(entities)      |
|          flush          |
| deleteInBatch(entities) |

* 代码举例

  ~~~java
  Users user = repository.findOne(1);	//传入Id

  System.out.println("userId=5是否存在：" + repository.exists(5));	//判断id是否存在

  ~~~

  

### JpaSpecificationExecutor接口的使用

* Specification封装了JPA Criteria查询条件

* extends  JpaSpecificationExecutor\<T>

  ~~~ java
  /**代码举例*/

  /**
  * root:查询类型（实体类）
  * criteriaQuery：查询条件
  * criteriaBuilder：构建Predicate
  * */
  //查询条件：age<18
  Specification<Users> specification = new Specification<Users>() {
  	@Override
  	public Predicate toPredicate(Root<Users> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder criteriaBuilder) {

  	// path: root (Users(age))
  	Path path = root.get("age");
  	return criteriaBuilder.lt(path, 18);   //less than 18岁
  	}
  	
  	Pageable pageable = new PageRequest(0,5);
  	//(根据搜查条件并结合分页功能)
  	Page<Users> page = repository.findAll(specification,pageable);
  };

  ~~~


