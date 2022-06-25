---
title: 'JVM内存分析-stack, heap and method area'
date: 2019-02-24 15:18:32
categories: Java
tags: [Java 300集, JVM, stack, head, 内存分析]
---

JVM的内存分为三个区域：
- 栈 stack
- 堆 heap
- 方法区 method area

<!--more-->
# Basic
## 栈 stack
- 方法执行的内存模型。是一个连续的内存空间。
- JVM为每个线程创建一个栈。栈属于线程私有，不能共享。
- 每个方法调用都会创建一个栈帧
- 特点： 先进后出，后进先出
	- 栈 相当于子弹夹
	- 栈帧 相当于子弹

## 堆 heap
- 存储对象
- JVM只有一个堆，被所有线程共享
- 不连续的内存空间。

## 方法区 method area
- 堆的一部分
- 存储类信息中不变或者唯一的内容
	- 代码
	- 静态变量
	- 静态方法
	- String常量

# 举例+图示
## 代码
```
class Student{
	String name;
	int age;
	void showAge() {
		System.out.println(name + " : " + age);
	}
}

public class JvmStackHeapSample {
	
	static int averageAge = 28;
	
	public static void main(String[] args) {
		Student student = new Student();
		student.name = "ryan";
		student.age = averageAge;
		student.showAge();
	}

	void anyMethod(){
		...
	}
}
```

## 分析
- 运行程序，首先使用方法区加载类信息
	- 代码
	- 静态变量
	- 静态方法
	- String常量
- 开始执行 main 方法，创建main()栈帧
- 创建局部变量 student = null
- 在堆里创建Student类对象，调用构造器初始化对象
- 将新建对象的地址赋值给student
- 将String常量“ryan”赋值给student.name
- 将静态变量averageAge赋值给student.age
- 调用student.show(),创建show()栈帧
- 打印: name + " : " + age
- 销毁show()栈帧
- 销毁main()栈帧

![JVM内存](/images/JVM内存分析.png)
