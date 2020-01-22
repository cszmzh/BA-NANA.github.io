---
layout: post
title: 'Linux学习之旅6——Vim编辑器与Shell脚本编程基础'
subtitle: '继Linux学习之旅5，下面学习Linux中常用的编辑器——Vim，以及Shell的一些基本语法，最后掌握使用Shell脚本编程的技能。'
date: 2019-11-16
categories: 技术
tags: Linux
comments: true
toc: true
---

# 目录

  * [一.Vim编辑器](#一vim编辑器)
  * [二.Shell编程](#二shell编程)
    * [1.第一个Shell脚本](#第一个shell脚本)
    * [2.Shell变量](#shell变量)
    * [3.Shell的数学运算](#shell的数学运算)
    * [4.Shell的环境变量](#shell的环境变量)
    * [5.Shell的参数变量](#shell的参数变量)
    * [6.Shell的选择语句](#shell的选择语句)
    * [7.Shell的条件测试](#shell的条件测试)
    * [8.Shell函数](#shell函数)

------



## 一.Vim编辑器

* 安装

  ~~~ 
  sudo apt install vim
  ~~~

* Vim的教程文档

  ~~~ 
  vimtutor
  ~~~

* 三种模式

  * 交互模式(Interactive Mode)：打开vim默认的模式，不能直接输入文本，但它可以让我们在文本间移动，删除文本，复制粘贴文本，跳转到指定行，撤销操作等。

    * 第一部分命令（复制、粘贴、删除、撤销等）

      ~~~ 
      该模式下第一部分命令：
      kjhl分别代表上下左右移动光标
      0 / $代表移动到行首/末尾
      w：移动到下一个单词

      x：删除一个字符（若要删除光标后几个字符，先输入个数，再按x即可）
      d：删除单词、行
      dd：删除行（先输入数字，再按下dd会删除光标所在行开始的n行）
      dw：删除单词
      d0：删除行首
      d$：删除行末

      yy：复制行到内存中
      yw：复制一个单词
      y$：复制光标到行末所有字符
      y0：复制光标到行首所有字符

      p：粘贴（在前面加上数字n，代表粘贴n次）

      r：替换一个字符（rs代表将当前光标所在字符换成s）
      R：替换模式，可以替换多个字符

      u：撤销操作
      Ctrl+R：取消撤销
      ~~~

    * 第二部分命令

      ~~~ 
      Shift+g（G）：跳转到文档最后一行
      gg：跳转到第一行
      行号+gg：跳转到指定行

      /：查找模式（用?代替表示从光标处向文件头搜索）
      :s 命令：查找并替换（-s/旧字符串/新字符串：替换光标所在行第一个旧字符串）
      :3,6 s/old/new/g （将3到6行匹配"old"的字符串替换为"new"，注意后面g参数）
      :%s/旧字符串/新字符串/g：替换文件中所有匹配的字符串

      :r 命令：合并文件(:r file.txt 代表在光标处插入该文件内容)
      ~~~

    * 第三部分命令

      ~~~ 
      :sp 命令：横向分屏 (sp: 另一个文件)
      :vsp 命令：垂直分屏

      Ctrl+W两次：从一个viewport移动到另一个viewport
      Ctrl+W+(k/j/h/l)：移动光标到上/下/左/右方的viewport

      Ctrl+W 接以下命令
      +：扩大当前viewport
      -：缩小当前viewport
      =：重新分配各个viewport占比
      r：调换各个viewport位置
      q/c：关闭当前viewport
      o：只保留当前viewport

      :! 命令：运行外部命令（例如:!ls）
      ~~~

      

  * 插入模式(Insert Mode)：按**字母键 i** 开启，这是我们熟悉的类似windows下记事本的模式，按ESC退出该模式

  * 命令模式(Command Mode)：按 **冒号键** 开启用该模式激活一些vim配置，还可以发送一些命令给终端

    ~~~ 
    #该模式下：
    #w 命令：保存文件(write)，例如：
    w file.txt

    #q 命令：退出vim编辑器，强制退出在前加!
    !q

    #x 命令：等同于wq命令，先保存文档后退出vim
    ~~~

    

    ![Linux-vim下的三种模式](../../../assets/img/Linux学习之旅6/Linux-vim下的三种模式.png)

## 二.Shell编程

* Shell是管理命令行的程序，是用户与操作系统间的桥梁

* chsh (Change Shell) 命令可以切换Shell

  ### 第一个Shell脚本

  ~~~ 
  #先创建一个Shell文本文件
  vim test.sh
  
  #文件中，指定一个Shell来运行，下面这行必不可少（以Bash为例）
  #!/bin/bash
  
  #注释（#开头即可）
  
  #运行脚本
  ./test.sh
  ~~~

* 调试程序

  ~~~ 
  #-x：以调试模式运行
  bash -x test.sh
  ~~~

* 将脚本放置在PATH变量中

  ~~~ 
  #先查看当前的PATH系统变量
  echo $PATH
  
  #然后将你的脚本文件放在path目录下，便可直接在任何目录运行
  ~~~

  

  ### Shell变量

  举例：

  ~~~ 
  message='Hello World'
  #上面message为变量名，值为Hello world
  #注意等号两边不加空格，若其中有转义字符：
  message=$'Hello,it\'s me'
  ~~~
  
  * echo命令：显示
  
    ~~~ 
    #将"hello"显示出来
    echo "hello"
    
    #插入换行符：-e与\n
    echo -e "First Line\nSecond Line"
    
    #脚本中显示变量：$与变量名
    echo $message
    ~~~
  
  * 根据引号类型，Bash的处理方式也不同
  
    | 类型      | 描述                                                         |
    | --------- | ------------------------------------------------------------ |
    | 单引号 '' | 单引号忽略包含的特殊字符，包括变量$                          |
    | 双引号 "" | 双引号忽略大多数字符，但不包括$ ， `(反引号)， \\ ( 反斜杠 ) |
    | 反引号 `` | 执行其包括起来的命令                                         |
  
    看一下例子增强理解：
  
    ~~~ 
    message='Hello'
    echo "The message is $message"
    输出结果：The message is Hello
    
    message=`pwd`
    echo "The directory is $message"
    输出结果：The directory is /home/ming
    ~~~
  
  * read 命令：请求输入
  
    ~~~ 
    #请求用户在终端中输入，可以输入多个变量
    read firstname secondname
    echo "Hello $firstname,$secondname!"
    
    #-p：提示信息
    read -p 'Please enter name:' name
    echo "Hello $name!"
    
    #-n：限制字符数目（8个字符为例）
    read -p 'Please enter name:' -n 8 name
    
    #-t；限制输入时间（秒）
    #-s：输入隐藏内容（例如密码）
    ~~~

  ### Shell的数学运算

  * let 命令：赋值
  
    ~~~ 
    let "a=1"
    let "b=8"
    let "c=a+b"
    echo "c=$c"
    
    #其余的运算与其他编程语言类似，不过多阐述
    #比较特殊的运算符：幂次方(**)，2的8次方示例：2**8
    ~~~

  ### Shell的环境变量

  * env 命令：显示所有环境变量
  
    ~~~ 
    #SHELL：表示目前所用的Shell
    #PATH：一系列路径的合集，脚本存放于这些路径可在任何目录直接执行
    #HOME：家目录路径
    #PWD：目前所在路径
    ~~~
  
  * export 命令：定义环境变量
  
    ~~~ 
    export name[=word]
    ~~~

  ### Shell的参数变量

  * 列举多变量
  
    ~~~ 
    ./variable.sh param1 param2 param3
    echo "The Second param is $2, there are $# params"
    
    #上述命令列举了多个参数，并使用以下属性
    $#：参数的数目
    $1：列举的第一个参数
    $2：第二个参数
    ...
    $n：第n个参数
    ~~~
  
  * shift 命令：变量移位
  
    ~~~ 
    echo "The first param is $1"
    shift
    echo "The first param is now $1"
    
    输出结果：
    The first param is param1
    The first param is now param2
    #使用shift命令后，$1对应第2个参数，$2对应第3个参数，以此类推
    ~~~
  
  * 数组
  
    ~~~ 
    #!/bin/bash
    array=('value0' 'value1' 'value2')
    array[3]='value3'
    echo ${array[0]}
    
    以上案例展示了如何定义数组，给数组赋值，显示数组的值
    若要一次显示数组中所有值，可以使用通配符：
    echo ${array[*]}
    ~~~
  
  ### Shell的选择语句
  
    * if-else语句
  
      ~~~ 
      #!/bin/bash
      name="BA_NANA"
      if [ $name = "BA_NANA" ]
      then
      		echo "Hello, $name!"
      else
      		echo "Please create a name!"
        fi
      
      #注意：Shell中的if语句使用一个=或两个=均可，与其他语言略有不同
      #if语句中，=左右要有空格，中括号左右要有空格，但赋值语句中=左右不允许有空格
      ~~~
  
      elif：否则（else if)
  
      ~~~ 
      if [ 条件1 ]
      then
      		语句1
      elif [ 条件2 ]
      then
      		语句2
      else
      		语句3
      fi
      ~~~



### Shell的条件测试

* 测试字符串

  | 条件                   | 描述                                 |
  | ---------------------- | ------------------------------------ |
  | \$string1 = \$string2  | 两个字符串是否相等 (区分大小写)      |
  | \$string1 != \$string2 | 两个字符串是否不相等                 |
  | -z \$string            | 字符串是否为空 (zero)，返回布尔类型  |
  | -n \$string            | 字符串是否不为空 (not)，返回布尔类型 |

* 测试数字

  | 条件                | 意义                              |
  | ------------------- | --------------------------------- |
  | \$num1 \-eq  \$num2 | 是否相等（equal）                 |
  | \$num1 \-ne  \$num2 | 是否不相等（not equal）           |
  | \$num1 \-lt  \$num2 | num1是否小于num2（lower than）    |
  | \$num1 \-le  \$num2 | num1是否≤num2（lower or equal）   |
  | \$num1 \-gt  \$num2 | num1是否大于num2（greater than）  |
  | \$num1 \-ge  \$num2 | num1是否≥num2（greater or equal） |

* 测试文件

  | 条件                 | 意义                        |
  | -------------------- | --------------------------- |
  | -e $file             | 文件是否存在（exist）       |
  | -d $file             | 是否为一个目录（directory） |
  | -f $file             | 是否为一个文件（file）      |
  | -L $file             | 是否为一个链接文件          |
  | -r/w/x $file         | 是否可读/可写/可执行        |
  | \$file1 \-nt \$file2 | 文件file1是否比file2新      |
  | \$file1 \-ot \$file2 | 文件file1是否比file2旧      |

* 逻辑与（&&）、逻辑或（\|\|）、非（!）

* case：测试多个条件

  ~~~ 
  #!/bin/bash
  
  case $1 in
  		"Tim" | "Tom")
  			echo "Hello, Tim or Tom!"
  			;;
  		"Marry")
  			echo "Hello, Marry!"
  			;;
  		"Zach")
  			echo "Hello, Zach!"
  			;;
  		*)
  			echo "I don't know you!"
  			;;
  esac
  ~~~

  * ;; 相当于主流编程语言中的break
  * *) 相当于else
  * 注意：case语句中的或是一个竖杠，不是两个

###Shell循环语句

* while循环

  ~~~ 
  while [ 条件 ]
  do
  	...
  done
  
  while [ 条件 ]; do
  	...
  done
  ~~~

* until循环

  ~~~ 
  until [ 条件 ]
  do
  	...
  done
  ~~~

* for循环-遍历列表

  ~~~ 
  for 变量 in 'value1' 'value2' ... 'valueN'
  do
  	...
  done
  ~~~

  使用举例：

  ~~~ 
  #!/bin/bash
  
  for file in `ls`
  do
  	echo "File found : $file"
  done
  ~~~

* for循环-常规

  ~~~ 
  for i in `seq 1 10`
  do
  	echo $i
  done
  #以上代码会输出i的值（1-10）
  
  for i in `seq 1 2 10`
  do
  	echo $i
  done
  #以上代码输出i的值（1-10，间隔为2，即1,3,5,7,9)
  ~~~


### Shell函数

* 定义

  ~~~ 
  函数名 () {
  		...
  }
  
  function 函数名 {
  		...
  }
  ~~~

* 传递参数

  ~~~ 
  #!/bin/bash
  
  print () {
  		echo "Hello, $1"
  }
  
  print Marry
  print Zach
  
  输出结果：
  Hello, Marry
  Hello, Zach
  ~~~

* 返回值（ $? 代表上一次命令运行的返回状态）

  ~~~ 
  #!/bin/bash
  
  print () {
  		echo Hello $1
  		return 1
  }
  
  print Zach
  echo "Return value of previous function is $?"
  ~~~

* 全局变量（global）与局部（local）变量

  ~~~ 
  #!/bin/bash
  
  local_global () {
  		local var1='local 1'
  		var1='new local 1'		#此处var1为函数内定义的局部变量
  		var2='global 2'		 #此处var2为函数外定义的全局变量
  }
  var1='global 1'
  var2='global 2'
  
  local_global
  ~~~

* 重载命令

  ~~~ 
  #!/bin/bash
  ls () {
  		command ls -lh
  }
  ls
  
  #注意：如果没有command这个关键字，程序会陷入无限循环
  ~~~

  ------
  
  到这里为止，Linux学习之旅（基础）就告一段落了，现在我们可以掌握Linux的常用操作和Shell编程的基础了，日后一定要多回顾知识，多加练习，早日成为精通Linux的大神！！！

