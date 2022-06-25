---
title: Array Vector Set & Map
date: 2018-11-10 11:55:40
categories: Java Basic
tags: [Java, basic, Array, Vector, Set, Map]
---

Java集合的一些基础知识。

# Array

An array stores a sequence of values

- same type
- primary type and String
- fixed size

<!--more-->

3 steps to create an array:

	int[] a;						// 1. declare
	a = new int[9];					// 2. create
	for (int i = 0; i < 9; i++) {	// 3. store values
		a[i] = i*10;
	}

example:

	String[] b = new String[]{"1", "2", "3"};
	String[] c = {"1", "2", "3"};

Two-dimensional arrays, e[1][2] = 23:

	int[][] e = {	{11,12,13},
					{21,22,23},
					{31,32,33}	};

# Vector

- can grow or shrink
- access its data using index
- **similar to ArrayList** but it is synchronised
- implements List

```
Vector<String> v = new Vector<>();	// initial capacity is 10.
```

## Vector VS ArrayList

### Synchronization

- Vector is synchronized, only one thread at a time can access the code (thread-safe)
- ArrayList is not synchronized, multiple threads can work on arrayList at the same time. (not thread-safe)

### Size
 - ArrayList increments 50% of the current array size
 - Vector increments 100%.

### Traversal
 - Vector: Enumeration and Iterator
 - ArrayList: Iterator

### Usage
ArrayList is newer and faster. usually choose ArrayList

# Set

- unordered collection of objects
- duplicate values cannot be stored

		Set<String> s = new HashSet<>();
		s.add("1");
		s.add("2");
		s.add("3");
		s.add("3");
		System.out.println(s);	// [1, 2, 3]
		s.get(0)	// error


# Map

Not a subtype of Collection

- mapping between key and value
- no duplicate keys allowed
- HashMap had no order, while TreeMap and LinkedHashMap have
