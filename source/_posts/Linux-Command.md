---
title: Linux Command
date: 2017-12-05 14:22:02
categories: Linux
tags: [Linux, command, shell]
---

## User & Password ##
We have below users and their password:
- root
- ryan (sudoer)
- grace
Login as ryan:

- check password expiration
```bash
chage -1 ryan	# number 1
```
<!--more-->
- change password
```bash
passwd			# for user self
```
- login as `root`
```bash
su - root
```
- change password for `grace`
```bash
passwd grace
```
- login as `grace`
```bash
su grace
```
- become `root`
```bash
sudo su - root
```
- set password never expire, must done by root
```bash
chage ryan
	
	Minimum password age:0
	Maximum password age:99999
	Last password change: enter
	Expiration warning: enter
	Password Inactive: enter
	Account expiration date: -1
```

## File & Directory ##

- check directory size
```bash
du -sh <path>
```
- monitor log
```bash
tail -100f <file>
```
- monitor log and catch specific content
```bash
tail -100f <file> | grep "<content>"
```
- change file modified time
```
touch  -d 20170101 <file>
```
- delete files that mtime > 7
```
find $dir -type f -mtime +7 -name '*.txt' -execdir rm -- '{}' \;
```
	1209-16:32  will delete files before 1201-16:32. Completed data for 7 days from 1202 to 1208
- copy and move
```
[cp / scp / mv] <originFile> <targetFile> / <targetDir>
scp ryan@IP:~/test.txt ~
```
- create file
```
touch <file>
```
- remove
```
rm <file>
rm -r <dir> # delete all the files and dirs in this dir (include)
```
- count number of files
```
fileNumber=`ls -1R <dir> | wc -l`
```
- zip and unzip
```
gzip <file>
gunzip *.gz
```
- permission
```
chmod 755 <file>
```

## Run Program ##
- nohup
```
cd ~/myApp
nohup ./runApp.sh &
```
	-1- runApp will be kept running at the background
	-2- the output like echo will be put into `~/myApp/nohup.out`
- job process id
```
jobPid="$(ps -ef | grep ksh | grep runApp.sh | awk '{print $2}')"
```
- kill job
```
kill -9 <jobPid>
```
- pass parameter
```
runApp.sh xyz	# so in runApp.sh, $1="xyz"
```

## Operation ##
- if else
```
if [""=""]; then
	action
else
	action
fi
```

## Compile / Build Java Program ##
```bash
releasePath=~/myApp
buildPath=$releasePath/application
~/apache-ant/bin/ant -Dreleasepath=$releasePath -Dbuildpath=$buildPath -Dant.project=<appName> -Dlibpath=$releasePath/lib

status=$?
if [$status -eq 0]; then
	echo "compilation is successful.."
else
	echo "compilation is unsuccessful.."
fi
```


## Others ##
- press to continue
```
echo "Press to continue.."
read q
```

