---
title: JUnit - Beginner
date: 2018-02-14 07:55:55
categories: JUnit
tags: [JUnit, test, unit, beginner, automation]
---

Notes, Samples for JUnit.

<!--more-->
# Sample 1

## Class to be tested:
```
package test;

public class Calculator {
	public static int add(int num1, int num2) {
		return num1 + num2;
	}

	public static int sub(int num1, int num2) {
		return num1 - num2;
	}
}
```

## Test Case:
```
package test;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotEquals;

import org.junit.Test;

public class CalculatorAddTest {

	@Test
	public void pass() {
		assertEquals("Error in add()-1..", 3, Calculator.add(1, 2));
		assertEquals("Error in add()-2..", -3, Calculator.add(-1, -2));
		assertEquals("Error in add()-3..", -1, Calculator.add(-1, -2));
	}
	
	@Test
	public void fail() {
		assertNotEquals("Error in add()-4..", 0, Calculator.add(1, 2));
	}
	
}
```
Run this by: **Run As - JUnit Test**


## Test Standalone or Test Runner
```
package test;

import org.junit.runner.JUnitCore;
import org.junit.runner.Result;
import org.junit.runner.notification.Failure;

public class RunTestStandalone {
	public static void main(String[] args) {
		Result result = JUnitCore.runClasses(CalculatorAddTest.class);
		for (Failure failure : result.getFailures()) {
			System.out.println(failure.toString());
		}
		System.out.println("Result Pass? : " + result.wasSuccessful());
	}
}
```
Run this by: **Run As - Java Application**

## Test Suite
```
import org.junit.runner.RunWith;
import org.junit.runners.Suite;
import org.junit.runners.Suite.SuiteClasses;

@RunWith(Suite.class)
@SuiteClasses({ CalculatorAddTest.class, CalculatorSubTest.class })
public class AllTests {

}
```
Run this by: **Run As - JUnit Test**

# Sample 2

## Testing class:
```
public class Animal {
	String name;
	double speed;

	public Animal(String name, double speed) {
		this.name = name;
		this.speed = speed;
	}

	public String getName() {
		return name;
	}

	public double getSpeed() {
		return speed;
	}

	public double walkSpeed () {
		return speed;
	}

	public double runSpeed() {
		return speed*2;
	}
}

```

##