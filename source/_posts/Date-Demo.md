---
title: Date Demo
date: 2019-03-05 23:13:32
categories: Java
tags: [Java 300集, DateFormat, Date, Calendar]
---
# DateFormat
## get date string in specific format
```java
Date date = new Date();
DateFormat dateFormat = new SimpleDateFormat("dd-MMM-yyyy");
String dateString = dateFormat.format(date);
System.out.println(dateString); // 05-Mar-2019
```


## get date from date string
```java
String dateString2 = "2019-03-05";
DateFormat dateFormat2 = new SimpleDateFormat("yyyy-MM-dd");
Date date2 = null;
try {
	date2 = dateFormat2.parse(dateString2);
} catch (ParseException e) {
	e.printStackTrace();
}
System.out.println(date2); // Tue Mar 05 00:00:00 SGT 2019
```

<!--more-->

# Calendar
```java
/**
 * 1st way : Wed Mar 06 00:00:00 SGT 2019
 */
Calendar calendar = new GregorianCalendar(2019, Calendar.MARCH, 6);
System.out.println(calendar.getTime());

/**
 * 2nd way : Mon Apr 15 10:49:07 SGT 2019
 */
Calendar calendar2 = new GregorianCalendar();
calendar2.set(Calendar.YEAR, 2019);
calendar2.set(Calendar.MONTH, Calendar.APRIL);
calendar2.set(Calendar.DATE, 15);
calendar2.set(Calendar.HOUR_OF_DAY, 10);
System.out.println(calendar2.getTime());

/**
 * 3rd way : Wed Mar 06 13:49:07 SGT 2019
 */
Calendar calendar3 = new GregorianCalendar();
calendar3.setTime(new Date());
System.out.println(calendar3.getTime());

/**
 * function
 */
calendar.add(Calendar.MONTH, 10);
calendar.add(Calendar.DATE, -10);
```