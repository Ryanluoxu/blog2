---
title: IO Stream
date: 2019-04-14 22:10:47
categories: Java
tags: [Java 300集, IO, copy, 序列化, 装饰设计模式]
---

# Bsic

## Summary
![IO-stream](/images/IO-stream.jpg)

<!--more-->

## Example
### Read Fie
```java
private static void readFile() throws FileNotFoundException, IOException {
	String filePath = "C:/Users/luoxu/Documents/temp.md";
	BufferedReader reader = new BufferedReader(new FileReader(new File(filePath)));
	String currentLine;
	while ((currentLine = reader.readLine())!=null) {
		System.out.println(currentLine);
	}
	reader.close();
}
```

### Write File
```
private static void writeFile() throws IOException {
	String filePath = "C:/Users/luoxu/Documents/temp.md";
	BufferedWriter writer = new BufferedWriter(new FileWriter(new File(filePath), true));
	String stringAppended = "Current Time --> " + (new Date()).toString();
	writer.newLine();
	writer.append(stringAppended);
	writer.flush();
	writer.close(); 
}
```

### Copy File 
```
private static void copyFile() throws FileNotFoundException, IOException {
	/**
	 * copy file
	 * 1. source and destination
	 * 2. get in and out stream
	 * 3. move data from in to out, one buffer by one buffer
	 * 4. flush
	 * 5. close stream
	 */
	String srcFile = "C:/Users/luoxu/Pictures/chloe1.jpg";
	String dstFile = "C:/Users/luoxu/Pictures/chloe2.jpg";
	BufferedInputStream in = new BufferedInputStream(new FileInputStream(new File(srcFile)));
	BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream(new File(dstFile)));
	byte[] buffer = new byte[1024];
	int size = 0;
	while(-1!=(size = in.read(buffer))) {
		System.out.println(size);
		out.write(buffer, 0, size);
	}
	out.flush();
	out.close();
	in.close();
}
```

# 序列化 - Serialize
- 序列化：将对象保存到文件或字节数组中
- 反序列化：将文件或字节数组转化成对象

- implements Serializable
- transient 的属性不被序列化 
## Example
```
public class Company implements Serializable{
	private static final long serialVersionUID = 1L;
	private String name;
	public Company(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
}
```

```
/**
 * 将对象序列化 - output
 */
String objectPath = "C:/Users/luoxu/Documents/object1";
Company company1 = new Company("Google");
ObjectOutputStream out = new ObjectOutputStream(new BufferedOutputStream(new FileOutputStream(new File(objectPath))));
out.writeObject(company1);
out.close();

/*
 * 	object1:
 * 	aced 0005 7372 001c 696f 2e72 7961 6e6c
	756f 7875 2e6a 6176 6133 3030 2e43 6f6d
	7061 6e79 0000 0000 0000 0001 0200 014c
	0004 6e61 6d65 7400 124c 6a61 7661 2f6c
	616e 672f 5374 7269 6e67 3b78 7074 0006
	476f 6f67 6c65 
 * 
 */

/**
 * 将文件反序列化成对象 - input
 */
ObjectInputStream in = new ObjectInputStream(new BufferedInputStream(new FileInputStream(new File(objectPath))));
Company company2 = (Company) in.readObject();
in.close();
System.out.println(company2.getName());	//Google
```

# 装饰设计模式

设计模式玩的是：类与类的关系
- 依赖：形参，局部变量
- 关联：属性
	- 聚合：不一致的生命周期
	- 组合：一致的生命周期
- 继承：子类父类
- 实现：实现接口

## Example
```
public class DecoratorDemo {

	public static void main(String[] args) {
		Speaker speaker = new Speaker();
		Amplifier amplifier = new Amplifier(speaker);
		speaker.speak();
		amplifier.speak();
		
		// speak with volume of 10
		// amplifier speak with volume of 1000
	}
	
}


class Speaker {
	int volume =  10;
	public void speak() {
		System.out.println("speak with volume of " + volume);
	}
}

class Amplifier{
	Speaker speaker;
	public Amplifier(Speaker speaker) {
		this.speaker = speaker;
	}
	public void speak() {
		System.out.println("amplifier speak with volume of " + speaker.volume*100);
	}
}
```