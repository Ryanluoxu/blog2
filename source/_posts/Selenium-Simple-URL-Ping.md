---
title: Selenium - Simple URL Ping
date: 2018-02-12 22:18:35
categories: Selenium
tags: [Selenium, Ping, URL, Login]
---

Use Selenium to create a program to
- open chrome browser
- open gmail.com
- login

<!--more-->

Download first:
1. [selenium-server-standalone-3.9.1.jar](http://www.seleniumhq.org/download/)
2. [chromedriver_win32](https://chromedriver.storage.googleapis.com/index.html?path=2.35/)

# UrlTestDemo
```
package main;

import java.util.concurrent.TimeUnit;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class UrlTestDemo {

	static WebDriver driver;
	
	public static void main(String[] args) {
		
		String chromedriver = "D:\\workspace\\RyanTool\\lib\\chromedriver.exe";
		String url = "https://accounts.google.com/signin/v2/identifier?service=mail&passive=true&rm=false&continue=https%3A%2F%2Fmail.google.com%2Fmail%2F&ss=1&scc=1&ltmpl=default&ltmplcache=2&emr=1&osid=1&flowName=GlifWebSignIn&flowEntry=ServiceLogin";
		String userId = "xxx";
		String password = "xxx";
		String userIdXpath = "//*[@id=\"identifierId\"]";
		String passwordXpath = "//*[@id=\"password\"]/div[1]/div/div[1]/input";
		String idNextBtnXpath = "//*[@id=\"identifierNext\"]/content/span";
		String pwdNextBtnXpath = "//*[@id=\"passwordNext\"]/content/span";
		
		System.setProperty("webdriver.chrome.driver",chromedriver);
		driver = new ChromeDriver();

		openUrl(url);
		input(userIdXpath, userId);
		click(idNextBtnXpath);
		input(passwordXpath, password);
		click(pwdNextBtnXpath);
		sleep(4);
		
		driver.close();
	}

	private static void sleep(int i) {
		try {
			TimeUnit.SECONDS.sleep(i);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}

	private static void click(String btnXpath) {
		driver.findElement(By.xpath(btnXpath)).click();
		sleep(1);
	}

	private static void input(String xpath, String content) {
		driver.findElement(By.xpath(xpath)).sendKeys(content);
		sleep(1);
	}

	private static void openUrl(String url) {
		driver.get(url);
		sleep(3);
	}

}
```