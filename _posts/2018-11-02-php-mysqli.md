---
layout: post
title: 'PHP:Mysqli预编译'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
cover: 'https://lixin.blog/assets/img/php_banner.png'
---

mysqli预编译执行sql语句

1. 把sql语句中要自定义的字段替换成"?"

2. mysqli对象的prepare方法创建预编译对象mysqli_stmt

3. 使用mysqli_stmt对象的bind_param方法，将要自定义的字符串变量传进去

4. 使用mysqli_stmt对象的excute方法执行sql语句

```php
    //需求：使用预编译方式向数据库添加三个用户
    //连接数据库
    $mysqli=new MySQLi("localhost","root","root","test");
    //修改字符集
    $mysqli->query("set names utf8");
    //创建预编译对象
    $sql="insert into user1 (name,password,age,birthday) values (?,?,?,?)";
    $mysqli_stmt=$mysqli->prepare($sql);

    //绑定参数1
    $name="zx";
    $password="123456";
    $age=28;
    $birthday="1989-08-08";
    //给？赋值，类型和顺序要对应
    $mysqli_stmt->bind_param("ssis",$name,$password,$age,$birthday);
    //执行
    $res1=$mysqli_stmt->execute();
    //判断结果
    if(!$res1){
        die("操作1失败".$mysqli_stmt->error);
        echo "<br/>";
    }else{
        echo "操作1成功<br/>";
    }

    //绑定参数2
    $name="zx2";
    $password="123456";
    $age=28;
    $birthday="1989-08-09";
    //给？赋值，类型和顺序要对应
    $mysqli_stmt->bind_param("ssis",$name,$password,$age,$birthday);
    //执行
    $res2= 
    //判断结果
    if(!$res2){
        die("操作2失败".$mysqli_stmt->error);
        echo "<br/>";
    }else{
        echo "操作2成功<br/>";
    }

    //绑定参数3
    $name="zx3";
    $password="123456";
    $age=28;
    $birthday="1989-08-03";
    //给？赋值，类型和顺序要对应
    $mysqli_stmt->bind_param("ssis",$name,$password,$age,$birthday);
    //执行
    $res3=$mysqli_stmt->execute();
    //判断结果
    if(!$res3){
        die("操作3失败".$mysqli_stmt->error);
        echo "<br/>";
    }else{
        echo "操作3成功<br/>";
    }

    //关闭连接
    $mysqli->close();
```

mysqli 处理查询结果集的几个方法

fetch_all() 

抓取所有的结果行并且以关联数据，数值索引数组，或者两者皆有的方式返回结果集。

fetch_array()

以一个关联数组，数值索引数组，或者两者皆有的方式抓取一行结果。

fetch_object() 

以对象返回结果集的当前行。

fetch_row() 

以枚举数组方式返回一行结果

fetch_assoc() 

以一个关联数组方式抓取一行结果。

fetch_field_direct() 

以对象返回结果集中单字段的元数据。

fetch_field() 

以对象返回结果集中的列信息。

fetch_fields()

以对象数组返回代表结果集中的列信息。

返回值格式用参数来定义，有三个

MYSQLI_ASSOC        关联数据	
MYSQLI_NUM		    数值索引
MYSQLI_BOTH	    	二者皆有

比如

```php
 $result = $link->query($sql);

 $data = $result->fetch_all(MYSQLI_ASSOC);

```