---
title: 黑马教程-Java Web Basic
date: 2018-07-07 21:17:58
categories: JavaWeb
tags: [java, web, heima, basic, tutorial]
---

这篇博客是黑马JavaWeb教程的学习笔记。

<!--more-->

# Java 基础
## Eclipse 的使用和断点调试
### JRE & Compiler
JRE version >= Java Compiler 
否则抛出`Bad version`异常

### Debug 断点调试
- 设置断点
- F5: step into
- F6: step over
- F7: step return
- drop to frame: 跳到当前方法第一行
- F8: 跳到下一个断点。
- 观察变量: 点击变量 - watch
- Breakpoints：全部断点视图。调试之后清除。

### Eclipse 快捷键
- 内容提示： alt+/
- 快速修复： ctrl+1
- 导包：	ctrl+shift+o
- 格式化代码块： ctrl+shift+f
- 向前向后： alt+方向键
- 查看方法说明： f2
- 重置透视图： Windows - Reset Perspective
- 更改大写： ctrl+shift+x
- 更改小写： ctrl+shift+y
- 复制一行： ctrl+alt+上下键
- 移动一行： alt+上下键
- 查看继承关系： ctrl+t
- 查看源码： ctrl+shift+t
- 查看快捷键： ctrl+shift+L

## JUnit 测试
1. 创建Person类
2. 创建测试类-TestPerson
3. 在 TestPerson 里，创建测试方法-testRun()
4. 加注释-@Test
5. 选择 testRun() - run as JUnit Test
6. 选择测试类，测试所有测试方法

### Before 和 After
```
@Before
public void before(){
	//初始化资源
	//所有测试方法前运行
}

@After
public void before(){
	//销毁资源
	//所有测试方法后运行
}

@BeforeClass
public static void beforeClass(){
	//每一个测试方法之前都运行
}

@AfterClass
public static void beforeClass(){
	//每一个测试方法之后都运行
}
```
### Assert 断言
```
public void testRun(){
	//通过：p.run() == "2"
	Assert.assertEquals("2",p.run());

	//不通过返回信息
}
```

## Java 部分特性
### 静态导入（不常用）
```
import static java.lang.System.out;
out.print("")
```

### 自动装箱、拆箱 
```
//装箱，自动将1变成一个object。
Integer i = 1;  //Interger i = new Integer("1")

//拆箱，自动将i对象拆成数字1.
int j = i; 
```

### 增强 for
只用于array，或者实现 Iterable 借口的集合上。

读取Map的两种方式：
1. 设置 keyset，通过iterator得到key，然后map.get(key)得到对应的值
	```
	// 传统方法
	Set set = map.keySet();
	Iterator it = set.iterator();
	while(it.hasNext()){
		String key = it.next();
		System.out.println(key + "->" +map.get(key));
	}
	
	// for each
	for(Object key : map.keySet()){
		System.out.println(key + "->" +map.get(key));
	}
	```
2. map的每一组key和value是一个entry。用entrySet()得到set。然后用iterator得到每一对entry。
	```
	// 传统方法
	Set set = map.entrySet();
	Iterator it = set.iterator();
	while(it.hasNext()){
		Map.Entry entry = it.next();
		System.out.println(entry.getKey() + "->" +entry.getValue());
	}

	// for each
	for(Object entry : map.entrySet()){
		System.out.println(entry.getKey() + "->" +entry.getValue());
	}
	```

HashMap 没有顺序，需要顺序用 LinkedHashMap();

For Each 需要注意的问题：
1. 只是取数据，不用于赋值。需要修改数据，用传统for方式

### 可变参数
可变参数视为数组。

```
public int sum(int ...nums){
	int sum = 0;
	for(int i:nums){
		sum+=i;	
	}
}

public void testSum(){
	sum(1,2,3,4,5,6); // sum(array);
}

int nums[] = {1,2,3,4}  // nums 只是一个对象
Integer nums[] = {1,2,3,4}  // 这时是多个对象

// 在可变参数里，比如 “T...a”，要求参数为多个对象。
```

### 枚举类
限定了方法的使用对象。
```
// 定义枚举的构造函数（私有），方法和字段。来封装信息。
enum Grade(){	// class
	A("90-100"),B("80-89"),C("70-79");		// Object

	private String value;  // 封装每个对象对应的分数
	private Grade(String value){
		this.value = value;	
	}
	public String getValue(){
		return this.value;
	}
}

public void print(Grade g){
	// 使用自己定义的枚举类
	System.out.println(g.getValue());
}

@Test
public void testPrint(){
	print(Grade.B);
}

```

带抽象方法的枚举

```
// 要求调用枚举对象时，需要定义优良中。
enum Grade(){
	A("90-100"){
		public String rank(){
			return "优";	
		}
	};
	B("80-89"){
		public String rank(){
			return "良";	
		}
	};
	C("70-79"){
		public String rank(){
			return "中";	
		}
	};	

	// 抽象方法
	public abstract String rank();

	// 封装数值
	private String value;
	private Grade(String value){
		this.value = value;	
	}
	public String getValue(){
		return this.value;
	}
}

public void print(Grade g){
	// 使用自己定义的枚举类
	System.out.println(g.getValue());
	System.out.println(g.rank());
}

@Test
public void testPrint(){
	print(Grade.B);
}

@Test
public void testEnum(){
	Grade.C.name();	 //得到C
	Grade.C.ordinal();  //得到位置3
	
	Grade.valueOf(“B”);	// 返回一个枚举对象。可以用于检测得到的值是否是有效值。
	
	Grade.values();  // 返回枚举的所有值
}
```

枚举只有一个枚举值时，可以当作单态设计模式。
```
// 用单态设计模式定义一个类
enum Person{
	A;
}
```

## 反射
1. 加载类
	1. Class 代表某个类的字节码， Class.forName(String className)
2. 解剖出类的各个部分
3. 用得到的各个部门执行相应的任务

```
// 加载所需类的字节码到内存
Class classt = Class.forName("io.ryan.demo.Person");

// 加载的另外两个方法
Class classt = new Person().getClass();
Class classt = Person.class;

// 解剖每个部分
class.getConstructor	// 用于创建对象
class.getMethod			// 用于执行方法
class.getField			// 用于封装数据

class.getDeclaredConstructor
class.getDeclaredMethod
class.getDeclaredField

```
### 反射类的构造函数
例子1，无参构造
```
Class classt = Class.forName("io.ryan.demo.Person");
Constructor c = class.getConstructor();		// 选择某个构造函数
Person p = c.newInstance(); 	// 利用反射得到对象
```

例子2，有参构造
```
Class classt = Class.forName("io.ryan.demo.Person");
Constructor c = class.getConstructor(String.class);		// 接收 Class 类
Person p = c.newInstance(“A String”);
```

例子3，私有构造
```
Class classt = Class.forName("io.ryan.demo.Person");
Constructor c = class.getDeclaredConstructor(List.class);	// private
c.setAccessible(true); 	// 暴力反射，破解私有
Person p = c.newInstance(new ArrayList());
```

例子4，创建对象的另一个途径
```
Class classt = Class.forName("io.ryan.demo.Person");
Person p = class.newInstance();  // 只限有无参的构造函数
```

### 反射类的方法

- public
- static: 无需传入对象，null
- private： 需要暴力破解

```
Class classt = Class.forName("io.ryan.demo.Person");
Method method = classt.getMethod(methodName, 参数.class);	// 得到方法
method.invoke(ojb,args)		// 指定对象来执行这个方法

```

- main
```
Class classt = Class.forName("io.ryan.demo.Person");
Method method = classt.getMethod(“main”, String[].class);
// 看到数组，就要拆。
method.invoke(null, (Object)new String[]{"aa","dd"});

```

### 反射类的字段（属性）
- public
- private
- static

```
Class classt = Class.forName("io.ryan.demo.Person");
Field f = classt.getField("name");
f.getType(); 	// 得到该字段的类型
f.get(p1);		// 得到 p1 的名字。

f.set(p1,"xxx")	// 赋值
```

## 内省 introspector

用于获取或设置JavaBean的属性。
- Bean的属性由get/set方法决定。
- getBeanInfo()，将bean的属性都省到BeanInfo里。
- getPropertiesDescriptor()

```
// 得到bean的所有属性
BeanInfo info = Introspector.getBeanInfo(Person.class, Object.class); // 不要Object属性
PropertyDescriptor[] pds = info.getPropertyDescriptors()
for (PropertyDescriptor pd:pds){
	System.out.println(pd.getName());
}

// 操纵bean的指定属性
PropertyDescriptor pd = new PropertyDescriptor("age", Person.class);
Method method = pd.getWriteMethod();	// 相当 setAge()
method.invoke(p1, 30);

Method method2 = pd.getReadMethod();
method2.invoke(p1, null);	// 相当 getAge()

// 获取当前操作的属性的类型
PropertyDescriptor pd = new PropertyDescriptor("age", Person.class);
pd.getPropertyType();

```

## beanUtils操纵bean属性
- 导入beanUtils包.
- 导入logging包.


```
Person p1 = new Person();
BeanUtils.setProperty(p1, "name", "ryan");

// 数据(String)来了，放到对象里去。包括自动转：8种基本类型。
String name = "ryan"
BeanUtils.setProperty(p1, "name", name);

// 以外的数据类型，会抛出异常
String birthday = "1988-09-09";
BeanUtils.setProperty(p1, "birthday", birthday);	//Date类型，所以报错

//需要注册Date转换器
Converter converter = new Converter(){
	public Object convert(Class type, Object value){
		if(value==null){
			return null;
		}
		if(!value instanceof String){
			throw new ConversionException("参数不是String.");
		}
		String str = (String) value;
		if(str.trim().equal("")){
			return null;
		}
		SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");
		try{
			return df.parss(str);	
		} catch(ParseException e){
			throw new RuntimeException(e);	//异常链不能掉
		}
	}
}
ConvertUtils.register(converter, Date.class);
BeanUtils.setProperty(p1, "birthday", birthday);	//就不会有异常

// 查看文档会发现，Converter已经有很多现成的实现类。
ConvertUtils.register(new DateLocaleConverter(), Date.class);
BeanUtils.setProperty(p1, "birthday", birthday);	//有bug

// 用map的值填充bean属性
Map map = new HashMap():
map.put("name","ryan");

ConvertUtils.register(new DateLocaleConverter(), Date.class);
BeanUtils.populate(bean, map); 	//填充
```

## 泛型 Generic

相当一个类型的变量。
在编写集合时，限制了集合的处理类型，从而把运行问题，转为编译问题。
```
// 声明的变量是String的集合。
List<String> list = new ArrayList<String>();
```

泛型的使用：
- List<E> 泛型类，List<String>参数化的类
- Iterator<E>
- Map<K,V>
```
List<String> list = new ArrayList<String>();
//...

Iterator<String> it = list.iterator();
String value = it.next(); // 默认就是String，不需要强转。 

// for each
for( String s : list){...}
```

Map
```
Map<int, String> map = new HashMap<int, Stirng>();
//...

Set<Map.Entry<Integer,String>> set = map.entrySet();

for(Entry<Integer,String> entry : set) {
	entry.getKey();
	entry.getValue();
}
```

### 注意的问题：
- 泛型的对象不能是基本类型。
- new时，前后泛型要一致。
- 泛型是给java编译器用的，编译后的binary不带泛型信息。


### 自定义泛型方法：
```
// 调用之前先声明
public <T> void gMethod(T t){..}
public <T,K> void gMethod(T t, K k){..}

// 类上声明泛型
public class gClass<T,K>{
	public void gMethod(T t, K k){...}

	// 静态是无效的
	public static void gMethod(T t, K k){...} 	// 错误
	public static <T,K> void gMethod(T t, K k){...}
}
```

例子：泛型方法-实现指定位置上的数组元素的交换
```
public <T> void swap(T arr[], int pos1, int pos2){
	T tmp = arr[pos1];
	arr[pos1] = arr[pos2]
	arr[pos2] = tmp;
}
```

例子：泛型方法-接收任意数组，颠倒所有元素
```
public <T> void reverse(T arr[]){
	int start = 0;
	int end = arr,length-1;
	
	while(true){
		if(start>=end){ break; }
	
		T tmp = arr[start];
		arr[start] = arr[end]
		arr[end] = tmp;
		
		start++;
		end--;
	}	
}
```

例子：泛型类
```
public class gClass<T>{
	private T t;
	public void setT(T t){...}
	public T getT(){...}
}
```

# XML 运用

- 描述数据
- 程序的配置文件

## XML 语法

### 文档声明
```
<?xml version="1.0" encoding="UTF-8" ?>
```

### 元素：标签
- 包含标签体：<a>hello</a>
- 不包含标签体：<a></a> 或者 <a/>
- 文档有且仅有一个根标签，其他都是子标签。
- 标签体中的空格和换行都是有内容的。所以排版有时会影响到文档。
```
<a>hello</a>
<a>
	hello
</a>
```

### 属性
一个标签可以有多个属性，每个属性都有自己的名称和取值。
```
<input name="text">
```
相当于
```
<input>
	<name>text</name>
</input>
```

### 注释

`<!-- 注释 -->`

### 转义字符
- <	:	&lt;
- >	:	&gt;
- &	:	&amp;
- "	:	&quot;
- '	:	&apos;

### 处理指令-PI
指明该文档用哪个css来执行。
```
<?xml version="1.0" encoding="UTF-8" ?>
<?xml-stylesheet type="text/css" href="xxx.css"?>
```

## XML 约束

编写一个文档来约束另一个文档。用于框架的编写。

### XML DTD （Doc Type Definition)
book.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE shelf SYSTEM "book.dtd">	// 指明被谁约束，声明所有shelf内标签都要遵循dtd文档
<shelf>
	<book>
		<name>Java..</name>
		<price>100.00</price>
	</book>
</shelf>
```
book.dtd
```
<!ELEMENT shelf(book+)>		// shelf 只可以有 book 标签
	<!ELEMENT book(name,price)>		// book 里包含 name 和 price 标签
	<!ELEMENT name(#PCDATA)>	// 	name 标签里包含字符串
	<!ELEMENT price(#PCDATA)>
```

引用公共文档
```
<!DOCTYPE web-app PUBLIC "<URL>"> // 本地文档 -> SYSTEM 
```

元素定义
```
<!ELEMENT shelf(book+)>		// （ ）里为元素内容
<!ELEMENT price(#PCDATA)>	// （ ）里为元素类型，EMPTY，ANY

<!ELEMENT book(name,price)>	//	name 和 price 必须依次出现
<!ELEMENT book(name|price)>	//	name 和 price 任选其一

<!ELEMENT shelf(book+)>		// 一次或多次
<!ELEMENT shelf(book？)>		// 零次或一次
<!ELEMENT shelf(book*)>		// 零次或多次
```

属性定义
```
<!ATTLIST 元素名/标签名
	属性名1 属性值类型 设置说明
	属性名2 属性值类型 设置说明
>

<!ATTLIST product
	name CDATA #REQUIRED	// 必选
	color CDATA #IMPLIRED	// 可选
>
```
-  CDATA
-  ENUMERATED
-  ID
-  ENTITY：为一段内容创建一个别名，用于引用

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE contacts[
	<!ELEMENT contacts ANY>
	<!ELEMENT contact(name, email)>
	<!ELEMENT name(#PCDATA)>
	<!ELEMENT email(#PCDATA)>

	<!ATTLIST contact serial_number ID #REQUIRED>
]>

<contacts>
	<contact serial_number="a1">	// ID 不能数字开头
		<name>ryan</name>
		<email>ryan@mail.com</email>
	</contact>
	<contact serial_number="a2">
		<name>tim</name>
		<email>tim@mail.com</email>
	</contact>
</contacts>
```

实体定义
- 引用实体
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE book[
	<!ENTITY bookname "Java Web Development"

	<!ELEMENT book(name)>
	<!ELEMENT name(#PCDATA)>
]>

<book>
	<name>&bookname</name>
</book>
```
- 参数实体: 代码重用 
```
<?xml version="1.0" encod ing="UTF-8" ?>
<!DOCTYPE contact[
	<!ENTITY % tag "name,email,address"

	<!ELEMENT contact(%tag)>
]>
```

### XML Schema

## XML 编程（CRUD）

解析方式：
- dom: doc obj model
- sax: simple Api for xml
- 解析开发包：Jaxp, Jdom, dom4j

### dom
- xml文档解析成 element，attribute 和 text 节点(Node)对象。
- 方便 crud
- 占用内存大

### sax
- 读一行，处理一行
- 只适合读取，不适合cud

### 调整jvm内存
```
// jvm 默认占用 xxM
byte b[] = new byte[1024*1024*1024]	// 占用1G
```
设定jvm占用100m内存： 
`Arguments - VM argument: -Xmx100m`

### Jaxp对xml解析
```
// 1 创建factory
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();

// 2 得到 dom builder
DocumentBuilder builder = factory.newDocumentInstance();

// 3 解析xml文档，得到代表文档的document
Document doc = builder.parse("src/book.xml");

```

### dom 读
XML文档例子：
```
<shelf>
	<book name="xxx">
		<price>???</price>
	</book>
</shelf>
```
使用dom对xml文档r
```
// 读取一个
NodeList nodeList = doc.getElementsByTagName("price");
Node node = nodeList.item(0);
String content = node.getContent();

// 遍历，递归，得到所有标签
Node root = doc.getElementsByTagName("shelf").item(0);
printAll(root);

public void printAll(Node node){
	if(node instanceof Element){
		System.out.println(node.getNodeName());
	}
	NodeList nodeList = node.getChildNodes();
	for(Node tmpNode:nodeList){
		printAll(tmpNode);
	}	
}

// 得到xml文档中标签属性的值
Node bookName = doc.getElementsByTagName("book").item(0);
// 强转
Element bookName = doc.getElementsByTagName("book").item(0);
bookName.getAttribute("name")
```

### dom 增
```
// 添加： <price>$$$</price>

// 创建节点
Element price = doc.createElement("price")
price.setTextContent("$$$");

// 把节点挂到book下面
Element book = (Element) doc.getElementsByTagName("book").item(0);
book.appendChild(price);

// 更新好内存中的 doc，需要再写回到文档中
TransformerFactory tfFactory = TransformerFactory.newInstance();
Transformer tf = tfFoctory.newTransformer();
tf.transfrom(new DOMSource(doc), new StreamResult(new FileOutputStream("src/book.xml")));

// 加属性 <book name="xxx" color="blue">
Element book = doc.getElementsByTagName("book").item(0);
book.setAttribute("color","blue");
```

### dom 删
```
// 得到要删除的节点
Element price = doc.getElementsByTagName("price").item(1);

// 得到要删除节点的上级节点
price.getParentNode().removeChild(price);
```

### dom 改
```
// 得到要更改的节点
Element price = doc.getElementsByTagName("price").item(1);

// 更改
price.setTextContent("100");
```

### XML 编程实例：考生成绩管理系统

- 添加用户
- 删除用户
- 查询成绩

```
<student id="xxx">
	<name>xxx</name>
	<location>xxx</location>
	<score>xxx</score>  
</student>
```

模块设计：
- Main.java (A,B,C) - io.demo.ui
- StudentController.java (A,B,C) - io.demo.controller
- StudentDao.java (A,B,C)  - io.demo.dao 
- 传输的数据都封装在student对象里 - io.demo.bean
- XmlUtils (getDoc, writeToDoc) - io.demo.util
- exam-system.xml (数据库）

代码 --> [xml-exam-system](https://github.com/Ryanluoxu/heima-java-web-course/tree/master/xml-exam-system)