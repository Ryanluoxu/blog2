---
title: Java Master - S10.Interface
date: 2018-02-11 22:55:20
categories: Java Master Course
tags: [Java, course, note, Interface]
---
Exersice for Interface of Completed Java Master Class.
<!--more-->

## Requirements
1. Interface ISaveable
	1. save() - return a list which contains values to be saved
	2. read(list) - DB provide a list of values, read these values and pass to the corresponding fields of the object
2. Player implements ISaveable: name, point, health, weapon
3. Game(main): 
	1. readValues - simulate getting values from DB and return a list with values
	2. saveObject(saveable Object) - save values of this object. return a list which will be saved to DB.
	3. loadObject(saveable object) - getting values and pass to the object
3. Game:
	1. create a player and save it
	2. load one player using fake values

## Codes
### ISaveable
```
package javamaster.interface_;

import java.util.List;

public interface ISaveable {
	List<String> save();
	void read(List<String> values);
}
```

### Player
```
package javamaster.interface_;

import java.util.ArrayList;
import java.util.List;

public class Player implements ISaveable{
	private String name;
	private int point;
	private int health;
	private String weapon;
	
	public Player(String name, int point, int health, String weapon) {
		super();
		this.name = name;
		this.point = point;
		this.health = health;
		this.weapon = weapon;
	}
	
	@Override
	public List<String> save() {
		List<String> values = new ArrayList<>();
		values.add(name);
		values.add(String.valueOf(point));
		values.add(String.valueOf(health));
		values.add(weapon);
		return values;
	}
	@Override
	public void read(List<String> values) {
		name = values.get(0);
		point = Integer.valueOf(values.get(1));
		health = Integer.valueOf(values.get(2));
		weapon = values.get(3);
	}

	@Override
	public String toString() {
		return "Player [name=" + name + ", point=" + point + ", health=" + health + ", weapon=" + weapon + "]";
	}
}
```

### Game
```
package javamaster.interface_;

import java.util.ArrayList;
import java.util.List;

public class Game {

	public static void main(String[] args) {
		ISaveable ryan;
		ISaveable eric;
		
		ryan = new Player("ryan", 100, 9900, "sword");
		saveObject(ryan);
		
		eric = new Player("", 0, 0, "");
		loadObject(eric);
		System.out.println(eric);
		
	}

	private static void loadObject(ISaveable eric) {
		List<String> values = readValues();
		System.out.println("Values to be loaded: ");
		for (String string : values) {
			System.out.println(string);
		}
		eric.read(values);
	}

	private static List<String> readValues() {
		// fake data
		List<String> values = new ArrayList<>();
		values.add("eric");
		values.add("200");
		values.add("6000");
		values.add("gun");
		return values;
	}

	private static void saveObject(ISaveable saveableObject) {
		List<String> values = saveableObject.save();
		System.out.println("Values to be saved: ");
		for (String string : values) {
			System.out.println(string);
		}
		System.out.println("");
	}	
}
```