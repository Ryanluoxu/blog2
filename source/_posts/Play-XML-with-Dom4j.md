---
title: Play XML with Dom4j
date: 2018-08-09 12:04:34
categories: JavaWeb
tags: [XML, dom4j, demo]
---

- Add, Delete, Update, Read
- Using XPath



# Add, Delete, Update, Read

## XML sample
```
<shelf> 
  <book name="Java Web"> 
    <name>Java Web</name>  
    <author>Ryan</author>  
    <price>99.0</price>  
  </book> 
  <book name="Spring"> 
    <name>Spring</name>  
    <author>AK</author>  
    <price>299.0</price>  
  </book> 
</shelf>
```
## Read
```
	@Test
	public void read() throws DocumentException {
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/book.xml");
		
		Element root = document.getRootElement();
		Element book = root.elements("book").get(0);
		String bookName = book.element("name").getText();
		System.out.println(bookName);
		
		String bookAttribute = book.attributeValue("name");
		System.out.println(bookAttribute);
	}
```
<!--more-->
## Add
```
	// Add content to XML
	@Test
	public void add() throws DocumentException, IOException {
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/book.xml");
		
		Element book = document.getRootElement().element("book");
		book.addElement("price").setText("199.0");
		
		OutputFormat format = OutputFormat.createPrettyPrint();
		
		XMLWriter writer = new XMLWriter(new FileWriter("src/book.xml"),format);
		writer.write(document);
		writer.close();
	}
	
	// Add content to XML in specific position
	@Test
	public void add2() throws DocumentException, IOException {
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/book.xml");
		
		Element book = document.getRootElement().element("book");
		List list = book.elements(); // [book, name, price]
		
		Element author = DocumentHelper.createElement("author");
		author.setText("Ryan");
		list.add(1, author);
		
		OutputFormat format = OutputFormat.createPrettyPrint();
		XMLWriter writer = new XMLWriter(new FileWriter("src/book.xml"),format);
		writer.write(document);
		writer.close();
	}
```
## Update
```
	@Test
	public void update() throws DocumentException, IOException {
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/book.xml");
		
		Element author = document.getRootElement().element("book").element("author");
		author.setText("Ryan Luo");
		
		OutputFormat format = OutputFormat.createPrettyPrint();
		XMLWriter writer = new XMLWriter(new FileWriter("src/book.xml"),format);
		writer.write(document);
		writer.close();
	}

```
## Delete
```
	@Test
	public void delete() throws DocumentException, IOException {
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/book.xml");
		
		Element book = document.getRootElement().element("book");
		List list = book.elements(); // [book, name, price]
		
		list.remove(4);
		
		OutputFormat format = OutputFormat.createPrettyPrint();
		XMLWriter writer = new XMLWriter(new FileWriter("src/book.xml"),format);
		writer.write(document);
		writer.close();
	}
	
	@Test
	public void delete2() throws DocumentException, IOException {
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/book.xml");
		
		Element author = document.getRootElement().element("book").element("author");
		author.getParent().remove(author);
		
		OutputFormat format = OutputFormat.createPrettyPrint();
		XMLWriter writer = new XMLWriter(new FileWriter("src/book.xml"),format);
		writer.write(document);
		writer.close();
	}
```


# using Xpath

## Read
```
	@Test
	public void getByXPath() throws DocumentException, IOException {
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/book.xml");
		
		String author = document.selectSingleNode("//author").getText();
		System.out.println(author);
	}
```

## Example: login
### XML sample
```
<users> 
  <user id="1" username="ryan" password="ryan"/>
  <user id="2" username="peter" password="peter"/>
  <user id="3" username="melvin" password="melvin"/> 
</users>
```
### Authorization
```
	@Test
	public void verify() throws DocumentException, IOException {
		
		String username="ryan";
		String password="ryan";
		
		SAXReader reader = new SAXReader();
		Document document = reader.read("src/users.xml");
		
		String xPath="//user[@username='"+username+"' and @password='"+password+"']";
		Node user = document.selectSingleNode(xPath);
		if(user==null) {
			System.out.println("Invalid credential..");
		} else {
			System.out.println("Successfully login..");
		}
	}
```