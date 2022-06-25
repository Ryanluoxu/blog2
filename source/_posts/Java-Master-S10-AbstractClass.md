---
title: Java Master - S10.AbstractClass
date: 2018-02-12 07:38:59
categories: Java Master Course
tags: [Java, course, note, Abstract，Abstract Class]
---
Notes and Exersice for Abstract Class of Completed Java Master Class.
<!--more-->

# Notes

Abstract class is a normal class but with abstract method.
```
public abstract class Animal {
	private String name;
	public Animal(String name) {
		this.name = name;
	}
	public String getName() {
		return name;
	}
	
	public abstract void eat();
}
```

1. Abstract class can not be instantiated. Abstract method needs to be implemented.
2. The class extends it have to implement all the abstract classes.
```
public class Dog extends Animal{
	
	public Dog(String name) {
		super(name);
	}

	@Override
	public void eat() {
		System.out.println(getName() + " is eating bones..");
	}
}
```
3. Abstract class can be extended by another abstract class
```
public abstract class Bird extends Animal{

	public Bird(String name) {
		super(name);
	}

	@Override
	public void eat() {
		System.out.println(getName() + " is eating ..");
	}
	
	public abstract void fly();
}
```
4. Need to be implemented by another class so that we can initiate it.
```
public class Chicken extends Bird{
	
	public Chicken(String name) {
		super(name);
	}

	@Override
	public void fly() {
		System.out.println(getName() + " is hardly flying..");
	}
}
```
5. main:
```
public class main {

	public static void main(String[] args) {
		
		Animal myDog = new Dog("funny");
		myDog.eat();
		
		//Animal myChicken = new Chicken("fat");  X no fly method
		Bird myChicken = new Chicken("fat");
		myChicken.eat();
		myChicken.fly();
	}
}
```
# Common and Difference between Abstract Class and Interface
## Common
1. Cannot instantiate them. Methods without an implementation

## Difference
1. Abstract class can declare fields that are not static and final
2. Abstract class can define public, protected and private concrete methods
3. Abstract class can extend only one parent class but implement multiple interfaces

The purpose of an abtract class is to provide a common definition of a base class that multiple derived classes can share.

# Exercise

## Requirements
- Create own LinkedList class
- the item can be added with auto sorting from small to large
- the item can be removed
- use traverse method to iterate and print all the items 

## Codes
### abstract class ListItem
```
public abstract class ListItem {

	// need to access by subclass in the same package
	protected ListItem leftLink;
	protected ListItem rightLink;
	private Object value;
	
	public ListItem(Object value) {
		this.value = value;
	}
	public Object getValue() {
		return value;
	}
	public void setValue(Object value) {
		this.value = value;
	}
	
	abstract ListItem next();
	abstract ListItem previous();
	abstract ListItem setNext(ListItem listItem);
	abstract ListItem setPrevious(ListItem listItem);
	
	abstract int compareTo(ListItem listItem);
}
```
### class Node extends ListItem
```
public class Node extends ListItem {

	public Node(Object value) {
		super(value);
	}

	@Override
	ListItem next() {
		return rightLink;
	}

	@Override
	ListItem previous() {
		return leftLink;
	}

	/**
	 * return listItem, so that simplify the steps: 
	 * currentItem.setPrevious(listItem).setNext(currentItem);
	 */
	
	@Override
	ListItem setNext(ListItem listItem) {
		rightLink = listItem;
		return listItem;
	}

	@Override
	ListItem setPrevious(ListItem listItem) {
		leftLink = listItem;
		return listItem;
	}

	@Override
	int compareTo(ListItem listItem) {
		return this.getValue().toString().compareTo(listItem.getValue().toString());
	}
	
}
```
### interface MyList
```
public interface MyList {

	ListItem getRoot();
	boolean addItem(ListItem listItem);
	boolean removeItem(ListItem listItem);
	void traverse(ListItem listItem);
}

```
### MyLinkedList implements MyList
```
public class MyLinkedList implements MyList {
	private ListItem root = null;

	@Override
	public ListItem getRoot() {
		return root;
	}

	/**
	 * sort in order
	 * 
	 * search from root
	 * if newItem is greater, go to next item until end
	 * if newItem is smaller, insert into
	 */
	
	@Override
	public boolean addItem(ListItem listItem) {
		if (root == null) {
			root = listItem;
			return true;
		} 
		ListItem currentItem = root;
		while (currentItem != null) {
			int comparison = currentItem.compareTo(listItem);
			if (comparison < 0) { // listItem is greater.
				if (currentItem.next()==null) {
					currentItem.setNext(listItem).setPrevious(currentItem);
					return true;
				} else {
					currentItem = currentItem.next();
				}
			} else if (comparison > 0) { // listItem is smaller
				if (currentItem.previous()==null) {
					currentItem.setPrevious(listItem).setNext(currentItem);
					root = listItem;
				} else {
					currentItem.previous().setNext(listItem).setPrevious(currentItem.previous());
					currentItem.setPrevious(listItem).setNext(currentItem);
				} 
				return true;
			} else {
				System.out.println("Duplicated item : " + listItem.getValue() );
				return false;
			}
		}
		return false;
	}

	@Override
	public boolean removeItem(ListItem listItem) {
		ListItem currentItem = this.getRoot();
		while (currentItem != null) {
			int comparison = currentItem.compareTo(listItem);
			if (comparison == 0) {
				System.out.println("Deleting item : " + listItem.getValue());
				if (currentItem.previous()==null) {
					if (currentItem.next()!=null) {
						root = currentItem.next();
						root.setPrevious(null);
					} else {
						root = null;
					}
					return true;
				}
				if (currentItem.next()==null) {
					currentItem.previous().setNext(null);
					return true;
				}
				currentItem.previous().setNext(currentItem.next()).setPrevious(currentItem.previous());
				return true;
			} else {
				currentItem = currentItem.next();
			}
		} 
		
		System.out.println("No existing: " + listItem.getValue());
		return false;
		
	}

	@Override
	public void traverse(ListItem listItem) {
		if (root == null) {
			System.out.println("Empty list..");
		} else {
			ListItem currentItem = root;
			while (currentItem != null) {
				System.out.println(currentItem.getValue());
				currentItem = currentItem.next();
			}
		}
		
	}
	
}
```
### main
```
/**
 * @author luoxu
 * 
 * Create own LinkedList class
 * the item can be added with auto sorting from small to large
 * the item can be removed
 * use traverse method to iterate and print all the items 
 */

public class Main {

	public static void main(String[] args) {

		MyList myList = new MyLinkedList();
		
		String testData = "2 3 1 2 5 6 7 4 9 8 7 4";
		String[] dataArray =  testData.split(" ");
		for (String data : dataArray) {
			myList.addItem(new Node(data));
		}
		
		myList.traverse(myList.getRoot());
		
		myList.removeItem(new Node("7"));
		myList.traverse(myList.getRoot());
		myList.removeItem(new Node("7"));
		myList.traverse(myList.getRoot());
		myList.removeItem(new Node("9"));
		myList.traverse(myList.getRoot());
		myList.removeItem(new Node("5"));
		myList.traverse(myList.getRoot());
		myList.removeItem(myList.getRoot());
		myList.traverse(myList.getRoot());
		myList.removeItem(myList.getRoot());
		myList.traverse(myList.getRoot());
		myList.removeItem(myList.getRoot());
		myList.traverse(myList.getRoot());
		myList.removeItem(myList.getRoot());
		myList.traverse(myList.getRoot());
	}

}
```

## Search Tree
### SearchTree implements MyList
```
public class SearchTree implements MyList{
	
	/**
	 * the difference: single direction
	 * starting from root to leaf, invalid for reverse
	 * 
	 */
	
	private ListItem root = null;

	@Override
	public ListItem getRoot() {
		return root;
	}

	@Override
	public boolean addItem(ListItem listItem) {
		if (root == null) {
			// empty tree, listItem become root
			root = listItem;
			return true;
		} 
		
		// otherwise start comparing
		ListItem currentItem = root;
		while (currentItem != null) {
			int comparison = currentItem.compareTo(listItem);
			if (comparison < 0) { 
				// listItem is greater, move right if possible
				if (currentItem.next()==null) {
					// no node to the right, so add at this point
					currentItem.setNext(listItem);
					return true;
				} else {
					currentItem = currentItem.next();
				}
			} else if (comparison > 0) { 
				// listItem is smaller, move left if possible
				if (currentItem.previous()==null) {
					// no node to the left, so add at this point
					currentItem.setPrevious(listItem);
					return true;
				} else {
					currentItem = currentItem.previous();
				} 
			} else {
				System.out.println("Duplicated item : " + listItem.getValue() );
				return false;
			}
		}
		return false;
	}

	@Override
	public boolean removeItem(ListItem listItem) {
		ListItem currentItem = this.getRoot();
		ListItem parentItem = currentItem;
		
		while (currentItem != null) {
			int comparison = currentItem.compareTo(listItem);
			
			if (comparison < 0) {
				parentItem = currentItem;
				currentItem = currentItem.next();
			} else if (comparison > 0) {
				parentItem = currentItem;
				currentItem = currentItem.previous();
			} else {
				// found item
				performRemove(currentItem, parentItem);
				return true;
			}
		} 
		
		System.out.println("No existing: " + listItem.getValue());
		return false;
	}

	private void performRemove(ListItem currentItem, ListItem parentItem) {
		System.out.println("Deleting:" + currentItem.getValue());
		if (currentItem.next()==null) {
			// no right branch, make parent link to the left branch
			if (parentItem.next() == currentItem) {
				// currentItem is the right child of parent
				parentItem.setNext(currentItem.previous());
			} else if (parentItem.previous() == currentItem) {
				// currentItem is the left child of parent
				parentItem.setPrevious(currentItem.previous());
			} else {
				// parentItem == currentItem which is root
				root = currentItem.previous();
			}
		} else if (currentItem.previous() == null) {
			// no left branch, make parent link to the right branch
			if (parentItem.next() == currentItem) {
				// currentItem is the right child of parent
				parentItem.setNext(currentItem.next());
			} else if (parentItem.previous() == currentItem) {
				// currentItem is the left child of parent
				parentItem.setPrevious(currentItem.next());
			} else {
				// parentItem == currentItem which is root
				root = currentItem.next();
			}
		} else {
			// both left and right exist
			// from the right branch, find the smallest value
			ListItem current = currentItem.next();
			ListItem leftMostParent = currentItem;
			while (current.previous()!=null) {
				leftMostParent = current;
				current = current.previous();
			}
			
			// now current is the smallest node in the right branch
			// put it into current deleting node
			currentItem.setValue(current.getValue());
			
			// delete smallest node
			if (leftMostParent == currentItem) {
				// no leftmost node, so current points to
				// the smallest node
				currentItem.setNext(current.next());
			} else {
				// set the smallest node's parent to point to 
				// the smallest node's right child
				leftMostParent.setPrevious(current.next());
			}
		}
	}

	@Override
	public void traverse(ListItem listItem) {
		// recursive method
		if (listItem != null) {
			traverse(listItem.previous());
			System.out.println(listItem.getValue());
			traverse(listItem.next());
		}
	}

}

```

### main
```
public class Main {
	
	public static void main(String[] args) {

		MyList myTree = new SearchTree();
		
		String testData = "2 3 1 2 5 6 7 4 9 8 7 4";
		String[] dataArray =  testData.split(" ");
		for (String data : dataArray) {
			myTree.addItem(new Node(data));
		}
		
		myTree.traverse(myTree.getRoot());
		
		myTree.removeItem(new Node("7"));
		myTree.traverse(myTree.getRoot());
		myTree.removeItem(new Node("7"));
		myTree.traverse(myTree.getRoot());
		myTree.removeItem(new Node("9"));
		myTree.traverse(myTree.getRoot());
		myTree.removeItem(new Node("5"));
		myTree.traverse(myTree.getRoot());
		myTree.removeItem(myTree.getRoot());
		myTree.traverse(myTree.getRoot());
		myTree.removeItem(myTree.getRoot());
		myTree.traverse(myTree.getRoot());
		myTree.removeItem(myTree.getRoot());
		myTree.traverse(myTree.getRoot());
		myTree.removeItem(myTree.getRoot());
		myTree.traverse(myTree.getRoot());
	}

}
```