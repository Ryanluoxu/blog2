---
title: Linux - Script - Menu Template
date: 2017-12-09 19:02:39
categories: Linux
tags: [Linux, sh, script, template, menu, center]
---
- menu always in the center of prompt window
- pre-define methods
	- print line
	- make choice
	- see you
	- press to continue

```bash
	printLine "+----------------------------------+"
	printLine "|        See you next time..       |"
	printLine "+----------------------------------+"
```

<!--more-->
```bash
#!/bin/ksh

#Head
option=0
errMsg=""
column=$(tput cols)
x=0
width=36
y=$((($column-$width)/2))
nextionAction=~/xxx.sh

printLine(){
	((x++))
	tput cup $x $y
	echo $1
}

makeChoice(){
	((x++))
	tput cup $x $y
	echo -n "Please choose (0-exit): "
	read option
}

seeYou(){
	((x++))
	printLine "+----------------------------------+"
	printLine "|        See you next time..       |"
	printLine "+----------------------------------+"
	printLine ""
}

PressContinue(){
	((x++))
	tput cup $x $y
	echo -n "Press to continue.. "
	read q
}

#Body
clear

while :
do
	#If error exists, display it
	if [$errMsg!=""]; then
		printLine "$errMsg"
	fi

	#show menu
	printLine "+----------------------------------+"
	printLine "|               Menu               |"
	printLine "+----------------------------------+"
	printLine "|                                  |"
	printLine "|            1. xxx                |"
	printLine "|                                  |"
	printLine "|            2. xxxxxx             |"
	printLine "|                                  |"
	printLine "+----------------------------------+"
	printLine ""

	makeChoice

	#clear the error message
	errMsg=""

	#logic
	case $option in
		0) seeYou; exit ;;
		1) $nextAction para1 ;;
		2) $nextAction para2 ;;
		*) errMsg="Invalid option. Try again.." ;;
	esac

	#set x back to 0 for next display
	x=0

done

```
