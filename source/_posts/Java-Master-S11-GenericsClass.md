---
title: Java Master - S11.GenericsClass
date: 2018-02-25 23:25:23
categories: Java Master Course
tags: [Java, course, note, generics]
---

Notes and Exersice for Generics Class of Completed Java Master Class.
<!--more-->

# Notes
## Help to find out error at compile time.
```
ArrayList myList = new ArrayList();
myList.add(1);
myList.add("Hi");
```
Here, different types of data will be added into myList. Error will occur at run time.
```
ArrayList<Integer> myList = new ArrayList<>();
myList.add(1);
myList.add("Hi");	// will show error
```
So error can be handled at compile time.

## Define type for class to avoid adding other type of data
```
Team<FootballPlayer> myTeam = new Team<>();
myTeam.add(new BasketballPlayer("mike")) // X Error
```
## Make sure T is correct type
- Team<T> will allow Team<String>
- But T here has to be Player
```
class Team<T extends Player> {}
```
- so that Team<String> shows error at compile time
- at the same time, no need to cast player inside class

## Make sure parameter is certain type
```
class Team<T extends Player> {
	void playWith(Team<T> team){..};
}
```
- T in Team<T> must be one of Player class
- when passing parameter, team will follow the same team type. 

# Demo: create own generics class
## Player
```
public class Player {

	private String name;

	public Player(String name) {
		super();
		this.name = name;
	}

	public String getName() {
		return name;
	}
	
}
```
## FootballPlayer
```
public class FootballPlayer extends Player{

	public FootballPlayer(String name) {
		super(name);
	}
}
```
## BasketballPlayer
```
public class BasketballPlayer extends Player {

	public BasketballPlayer(String name) {
		super(name);
	}
}
```
## Team
```
import java.util.List;

public class Team<T extends Player> {

	private String name;
	private List<T> members;
	
	public Team(String name) {
		super();
		this.name = name;
	}

	public String getName() {
		return name;
	}

	void addPlayer(T player) {
		members.add(player);
	}
}
```
## Main
```
public class GenericsMain {

	public static void main(String[] args) {

		BasketballPlayer michael = new BasketballPlayer("Michael");
		FootballPlayer beckham = new FootballPlayer("BeckHam");
		
		Team team1 = new Team("1st team"); // warning
		Team<String> team2 = new Team<>("2nd team"); // error 
		
		Team<Player> team3 = new Team<>("3rd team");
		team3.addPlayer(beckham);
		team3.addPlayer(michael);
		// able to add both types of player
		
		Team<FootballPlayer> team4 = new Team<>("4th team");
		team4.addPlayer(beckham);
		team4.addPlayer(michael);	// error
		// only accept football player
	}
}
```

