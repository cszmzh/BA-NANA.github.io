---
layout: post
title: 'JavaScript入门'
subtitle: '前后端必备知识'
date: 2019-09-10
categories: 技术
tags: 前端 JavaScript
cover: 'https://cdn.jsdelivr.net/gh/BA-NANA/ba-nana.github.io@1.0/assets/img/background-picture/JavaScript入门.png'
comments: true
toc: true
---



## 一.初识JavaScript

### 1.js输出

* window.alert()		弹出警告框

* document.write()         写到HTML文档中

* innerHTML                    写到HTML元素

* console.log()                  写到游览器的控制台

  ~~~ javascript
  <p id="one"></p>
  	<script type="text/javascript">
  		
  		window.alert("这是警告框中内容");
  		
  		document.write('helloworld');
  		
  		document.getElementById("one").innerHTML = "插入html元素";
  		
  		console.log("此内容将被输出到控制台");
  		
  	</script>
  ~~~

  

### 2.js语句与注释

* 分号

* JavaScript代码

* JavaScript语句标识符（var\if\for等等）

* 代码块

* 单行和多行注释

  ~~~ javascript
  //单行注释

  /**
  *多行
  *注释
  */
  ~~~


### 3.js数据类型

* 字符串（String）

* 数字（Number）

* 布尔（Boolean）

* 数组（Array）

  ~~~ javascript
  //数组
  		var cars = new Array();   //此时cars数组长度为0，使用cars.length获取数组长度
  		cars[0]="abcd";           //此时cars数组长度为1，故js数组是动态变化的
  		
  		var cars = new Array("abcd","efgh");
  		
  		var cars = ["abcd","efgh"];
  ~~~


* 对象（Object）

  ~~~ javascript
  //JavaScript对象
  		var person={
  			firstname:"Zach",
  			lastname:"James",
  			id:20185584
  		};
  //键值对
  //person.firstname
  //person["firstname"] 这两种方法均可以取出值
  ~~~

  

* 空（Null）

  ~~~ javascript
  var car="BWM";
  var car=null;	//用于清空变量
  ~~~

  

* 未定义（Undefined）

  + 表示变量不含有值



### 4.js变量

* 变量必须以字母开头（区分大小写）



### 5.js函数

* function a(参数){ }	并且声明会前置（和C语言不同）

* 匿名函数

  ~~~ javascript
  var c = function(cs1,cs2,cs3){
    console.log(cs1,cs2,cs3);
  }
  c(1,2,3);
  ~~~

* return

  ~~~ javascript
  function d(){
    return "this is return function";
  }
  var d1 = d();
  ~~~




## 二.JavaScript中的DOM操作

### 1.获取元素

* document.getElementById()

* document.getElementsByTagName()[]

* document.getElementsByClassName()[]

  注：后两个方法均可以数组下标确定具体的元素



### 2.修改元素

* innerHTML                             修改HTML元素

  ~~~ javascript
  var main = document.getElementById("intro");
  		main.innerHTML="helloworld";
  ~~~

* element.getAttribute()          得到属性值

* element.setAttribute()          设置属性值

  ~~~ javascript
  <div id="intro" data="nihao">123</div>
  ...
  var main = document.getElementById("intro");
  		main.innerHTML="helloworld";
  		alert(main.getAttribute("data"));	//接收data属性值
  		main.setAttribute("data","buhao");	//设置属性值
  		main.setAttribute("dd","hello,dd"); //还可以设置新的属性
  ~~~

* element.src                             修改图片

  ~~~ javascript
  <img src  = "1.jpg" id="image"/>
  ...
  var image = document.getElementById("image");
  image.src = "2.jpg";
  ~~~

* element.href                             修改跳转地址



### 3.修改CSS样式

~~~ javascript
<div id="main">helloworld</div>
	<script type="text/javascript">
		var main = document.getElementById("main");
		main.style.color="blue";
		main.style.fontSize="100px";
	</script>
~~~


### 4.DOM事件触发

* \<h1 onclick="...">点击该文本<//h1>

  ~~~ html
  <div onClick="alert('helloworld')">按钮</div>
  ~~~

* element.onclick=function(){...}

  ~~~ javascript
  var main = document.getElementById("main");
  		main.onclick=function(){
  			alert("main被触发了");
  		}
  ~~~

* element.addEventListener("click",function() {...}  )

  ~~~ javascript
  var btn = document.getElementById("btn");
  			btn.addEventListener("click",function(){
  				alert("btn被触发了");
  			});
  //建议使用这种方式
  ~~~




### 5.DOM节点

* document.createElement()                               //新增元素标签

* document.createTextNode()                             //新增文本

* parent.appendChild(child)

* parent.removeChild(child)

  ~~~ javascript
  <div id="div1">
  		<p id="p1">我是第一个p</p>
  		<p id="p2">我是第二个p</p>
  	</div>
  	
  	<script type="text/javascript">
  		var p = document.createElement("p");                //相当于新增<p></p>
  		var word = document.createTextNode("新增p标签");
  		p.appendChild(word);
  		
  		var div1 = document.getElementById("div1");
  		div1.appendChild(p);
  		
  		var p1 = document.getElementById("p1");
  		div1.removeChild(p1);
  		
  	</script>
  ~~~



## 三.JavaScript中Window对象

* window.open() -打开新窗口

* window.close() -关闭新窗口

* window.moveTo() -移动当前窗口

* window.resizeTo() -调整当前窗口尺寸

  ~~~ javascript
  <button onClick="openwindow()">创建窗口</button>
  	<button onClick="myFunction()">调整窗口</button>
  	<button onClick="moveFunction()">调整窗口</button>
  	<button onClick="closeFunction()">关闭窗口</button>
  	
  	
  	<script type="text/javascript">
  		var w;
  		
  		function openwindow(){
  			w = window.open('','','width=300,height=300');
  		}
  		
  		function myFunction(){
  			w.resizeTo(500,500);
  			w.focus();
  		}
  		
  		function moveFunction(){
  			w.moveTo(700,500);
  			w.focus();
  		}
  		function closeFunction(){
  			w.close();
  			w.focus();
  		}
  		
  		
  	</script>
  ~~~



### 1.window-screen

* screen.availWidth	//游览器可用宽度
* screen.availHeight       //游览器可用高度



### 2.window-location

* location.hostname                            //返回web主机的域名
* location.pathname                           //返回当前页面的路径和文件名
* location.protocol                               //返回web协议（http或https）
* location.href                                       //返回当前页面整个URL地址



### 3.window-history

* history.back()
* history.forward()                       //后退
* history.go()                                 //前进 0刷新 -1返回上一层 -2返回上两层