---
title: Notes_LinuxShellScriptsCookBook
date: 2018-05-21 23:24:51
categories: Linux
tags: [Linux, notes, shell, script]
---

Notes for << Linux Shell Scripts Cook Book>>.
It is very helpful for people with zero or little knowledge about Linux Shell Scripts.

<!--more-->

# Shell Something Out
## introduction
```bash
#!/bin/bash
# indicate using which shell interpretor

# when a shell starts, it initially define various setting from ~/.bashrc or ~/.bash_profile
```
## printing in the terminal
```bash
echo "hi \!"    # ! needs escape, otherwise means not operator
echo -n hello   # escape new line
echo -e "string contains escape sequence"   # when using escape sequence
echo -e "\e[31m this is red text \e[0m"    # \e[31m - red; \e[0m - reset
# reset = 0, black = 40, red = 41, green = 42, yellow = 43, blue = 44, magenta = 45, cyan = 46, and white=47

printf "%-5s %-10s %-4.2f \n" 11 ryan 99.88888
# printf without newline
# %-5s: string substitution + left alignment + width 5
# %-4.2f: float + 2 decimal
```
## variable and environment variable
```bash
env # view all the environment variables relative to a terminal
cat /proc/$PID/environ | tr '\0' '\n'
# environment variable for that process
# trade to get each variable one line

pgrep <program>

# equality vs assignment
var = value
var=value

# PATH is defined in /etc/environment or /etc/profile or ~/.bashrc
# store a list of paths where to search for executables, libraries
export PATH=$PATH:$NEW_PATH

# get length of variable
LENGTH=${#VAR}

# identify current shell
echo $SHELL
echo $0

# UID value of root is 0
echo $UID 
```
## math
```bash
n=10
let result=n+10
let n++
let n+=6

result=$[n+2]
result=$[$n+2]

result=$((n+5))

result=`expr 4+3`
result=$(expr n+5)

# bc for advanced
echo "4*0.53" | bc
echo "scale=2; 3/8" | bc    # semicolon as a delimiter

# base convention
n=100
echo "obase=2; $n" | bc # 1100100
echo "obase=10;ibase=2; $n" | bc # 4
echo "sqrt(100)" | bc
echo "10^10" | bc
```
## file description and redirection
```bash
# 0-stdin, 1-stdout, 2-stderr
# run echo $? immediately after the command execution to print the exit status
# 0 means successful completion

cat a* 2>err.txt    # 2 stands for stderr. so error will be redirect to err.txt
cat a* | tee out.txt | cat -n
# 1. get 2 stderr which show up and 2 stdout which go to next level 
# 2. tee will get 2 stdout into both out.txt and pipeline
# 3. cat get stdin and add line number
```
## arrays and associative arrays
```bash
array_var[0]='test1'
array_var[1]='test2'
echo ${array_var[0]}
echo ${array_var[*]}
echo ${array_var[@]}
echo ${#array_var[@]}   #2

# associate array
declare -A ass_array
ass_array[apple]=100

# list array indexes
echo ${!array_var[*]}
```
## visiting aliases
```bash
alias install='sudo apt-get install'
install docker # = sudo apt-get install docker
```
## grabbing info about the terminal
```bash
tput cols   # get width of terminal
tput lines  # get height of terminal
tput cup 100 100
tput setb n  # 0-7 background color, 7-grey
tput setf n  # 0-7 text color, 0-black
tput bold
tput smul
tput rmul   # underline

# hide password
echo -n "Enter password: "
stty -echo
read password
stty echo
echo
echo Password read.
```
## dates and delays
```bash
date +%s    # epoch time
date "+%d %B %Y" # 16 May 2018

# get time take for a program
start=`date +%s`
...
end=`date +%s`
let time_taken=end-start
```
- weekday:  %a Sat; %A Saturday
- Month:    %b Nov; %B November
- Day:      %d
- Date:     %D 10/20/99
- Year:     %y 10; %Y 2010
- Hour:     %I or %H    
- Minute:   %M
- Second:   %S
- Epoch:    %s

```bash
# delay scripts
echo -n Count:
tput sc     # tput sc to store the cursor position
count=0
while true;
do
    if [ $count -lt 40 ]; then
        let count++
        sleep 1;
        tput rc     # tput rc to restore the cursor position
        tput ed     # clean existed number
        echo -n $count
    else
        exit 0
    fi
done
```

## debugging the script
```bash
bash -x scripts.sh
# in scripts, using set -x and set +x to debug specific portion
# set -x : display arguments and commands
# set +x : disable debugging
# set -v : display input
# set +v : disable print input

## DEBUG
#!/bin/bash
function DEBUG()
{
        [ "$_DEBUG" == "on" ] && $@ || :
}

for i in {1..10}
do
        DEBUG echo $i
done

# #!/bin/bash -xv : enable debugging
```
## function and arguments
```bash
# function can return default 0 or any one argument if success
# check if a command terminated successfully
$CMD
if [ $? -eq 0 ];
then
    echo successfully 
else
    echo unsuccessfully
fi
```
## reading the output of a sequence of commands
```bash
# add line number
ls | cat -n > out.txt

# a subshell can be defined using () operation
# always quote them in double quotes to preserve the space and newline character
out="$(cat test.txt)"
```
## reading n characters without pressing the return key
```bash
read -n 2 var # read 2 chars from input
read -s password # read password in nonechoed mode
read -p " Enter : " var # display a message
read -t 2 var # read input within 2 sec
read -d ":" var # use delimiter to end input. hi:
```
## running a command until it succeeds
```bash
repeat(){ while :; do $@ && return; sleep 30; done }
repeat wget -c url
```
## field separations and iterators
```bash
# IFS internal field separator
data_set1="1 2 3 4 5"
for data in data_set1;
do
    echo $data
done

data_set1="1;2;3;4;5"
IFS=";" # so that system how to delimite data
for data in data_set1;
do
    echo $data
done

# for
for var in vars; # for i in {1..10}
do
    echo $var
done

# while
while condition
do 
done

# until
until [ $x -eq 9 ] # once meet, loop ends
do
done
```
## comparisons and tests
```bash
if condition;
then
fi

if condition;
then
else if condition; 
then
else
fi

# logical operators
[ condition ] && action; # action if condition true
[ condition ] || action; # action if condition fasle

# at least one space in []
[ $var -eq 0 ]
[ $var -nq 0 ]  # !=
# -gt, -ls, -ge >=, -le <=
[ $var -ne 0 -a $var2 -ne 0 ] # -a means and
[ $var -ne 0 -o $var2 -ne 0 ] # -o means or

[ -f $file ] # true if holds a file path or filename
[ -x $file ] # true if file is executable
[ -w $file ] # true if file is writable
[ -r $file ] # true if file is readable
[ -d $file ] # true if it is a directory
[ -e $file ] # true if it is existed file

# file exists sample
fpath="/etc/passwd"
if [ -e $fpath ]; then
    echo file exists.
else
    echo file not exists.
fi

# string comparison, use double square brackets, single brackets sometimes errors
[[ $str1 = $str2 ]] # space needed beside =
[[ -z $str ]] # true if str hols an empty string, str=""
[[ -n $str ]] # true if str hols an non-empty string, str="xx"

```
# Have a Good Command

## concatenating with cat
```bash
# cat can read both file and stdin
cat -T filename # display tab as ^I
cat -n filename # add line number, -b kill blank line
```
## record and play back of terminal sessions
```bash
# -t dump timing data to stderr
script -t 2>timing.log -a output.session
# ...
exit

scriptreplay timing.log output.session
```
## find file and file listing
```bash
find . -print   # delimit by \n
# ./1.txt
# ./2.txt
find . -print0  # delimit by \n
# ./1.txt./2.txt
find . -name "*.txt" -print
find . \( -name "*.txt" -o -name "*.pdf" \) -print  # (*.txt or *.pdf)
find  . -path "*/slynux/*" -print

# regex = regular expession, -i ignore case
find . -regex ".*\(\.py\|\.txt\)" # ".*.py|.txt"

# exclude !
find . ! -name "*.txt" -print

# level control
find . -maxdepth 2 ! -name "*.txt" -print # ./xxx/xxx
find . -mindepth 2 ! -name "*.txt" -print

# every object as a file. files: regular file, directory, character devices, block devices, symlinks, hardlinks, sockets, FIFO
find . -type d -print   
# d-dir, f-regular file, l-symbolic link, c-character special device, b-block device,s-socket, p-FIFO

# file times
# -atime : access time
# -mtime : modification time = content
# -ctime : change time = metadata (such as permissions or ownership)
# time in minutes ( -amin, -mmin, -cmin)
find . -type f -atime -7 -print # within last 7 days
find . -type f -atime 7 -print # exactly 7 days old
find . -type f -atime +7 -print # older than 7 days

# find files that newer than file.txt
find . -type f -newer file.txt -print

# file size over 2k, lt and eq; (M or G)
find . -type f -size +2k # -2k, 2k

# delete all files with .log
find . -type f -name "*.log" -delete

# based on permission, -perm
find . -type f -name "*.php" ! -perm 644 -print

# based on ownership, -user
find . -type f -user ryan -print

# using with exec \;
find . -type f -user root -exec chown ryan {} \;
# {} here will be replaced by the find result.

# exclude file
find . -name ".git" -prune

```
## xargs
```bash
# space is default delimiter, change to X 
echo "splitXsplitXsplitXsplit" | xargs -d X -n 2
# split split
# split split

echo "splitXsplitXsplitXsplit" | xargs -d X -n 2 ./show.sh
# show : split split
# show : split split
   
# use -I to specify a replacament string when xargs expands
cat args.txt | xargs -I {} ./show.sh {} -l
# each argument will be pass to replace {}

find . -type f -name "*.txt" -print0 | xargs -0 rm -f
# -print0 give one line output; -0 : delimiter is \0

cat files.txt | ( while read arg; do cat $arg; done )

```
## translat with tr
```bash
# only take stdin as input
tr [options] set1 set2

# uppler to lower
echo "HELLO WHO IS THIS" | tr 'A-Z' 'a-z'

# delete
cat file.txt | tr -d '<SET>'
cat file.txt | tr -d -c '0-9 \n' # complement; keep only 0-9, space and tab.
cat file.txt | tr -s ' ' # -s : squeeze repeating characters

# $[ operation ] performs a numeric operation

# character class
## alnum: Alphanumeric characters
## alpha: Alphabetic characters
## cntrl: Control (nonprinting) characters
## digit: Numeric characters
## graph: Graphic characters
## lower: Lowercase alphabetic characters
## print: Printable characters
## punct: Punctuation characters
## space: Whitespace characters
## upper: Uppercase characters
## xdigit: Hexadecimal characters
tr '[:lower:]' '[:upper:]'
```
## checksum and verification
```bash
md5sum filename
md5deep -rl directory_path > directory.md5 # -l : using relative path

find . -type f -print0 | xargs -0 md5sum > dir.md5
md5sum -c dir.md5
```
## cryptographic tool and hash
```bash
crypt PASSPHRASE <input_file >encrypted_file
crypt PASSPHRASE -d <encrypted_file >output_file

gpg -c filename # encrypt
gpg filename.gpg # decrypt

base64 filename > outputfile
base64 -d file > outputfile

# md5sum and sha1sum to verify password
# store password in hash format
# when user provide password, they convert to hash and compare

openssl passwd -1 -salt SALT_STRING PASSWORD

```
## sort unique and duplicate
```bash
sort file1.txt file2.txt > sorted.txt
sort -n file.txt    # numerical sort, -r reverse order, -M by months
sort -m sort1 sort2 # merge two sorted files
sort file1 file2 | uniq # find the unique lines
# check if a file is sorted
sort -C filename    # return 0 -> sorted

sort -nrk 1 data.txt    # -n numeric, -r reverse, -k specified the key, column 1
sort -nk 2,3 data.txt   # 1010dddddee, 01 is the key
# -b ignore leading blank lines
# -d sort in the dictionary order

sort unsorted.txt | uniq -u # show only unique lines
sort unsorted.txt | uniq -c # count how many times of each lines
sort unsorted.txt | uniq -d # show only duplicate lines
sort data.txt | uniq -s 2 -w 2 # -s 2 : ignore fisrt 2 char; -w 2 : max compare chars
# u:01:bash - only 01 will be compared

# xargs use space as default delimiter, if stdin contains space, need to use \0
uniq -z file.txt | xargs -0 rm
# -z zero-byte terminated output, -0 use \0 as delimiter
```
## temp file naming and random number
```bash
filename=`mktemp`
dirname=`mktemp -d`
tmpfile=`mktemp -u` # generate name without creating file
mktemp test.xxx # /home/ryan/test.NpW

```
## split files and data
```bash
split [COMMAND_ARGS] PREFIX
split -b 10k data.file -d -a 4 split_file # -d numeric suffixes, -a length
# split_file0001 split_file0002

split -l 10 data.file   # 10 lines for each file

# csplit : content based split
csplit server.log /SERVER/ -n 2 -s {*} -f server -b "%02d.log" ; rm
server00.log
# /SERVER/ : SERVER is the string to split
# -n 2 : the number of digit as suffix of file name
# -f server : prefix of filename, server01
# -b "%02d.log" : suffix format, filename = prefix + suffix, server01.log
# -s : silent without printing other messages
# server00.log will be beginning to the first SERVER which is empty

```
## slice filename based on extension
```bash
# ${VAR%.*} : remove the part that matches .*
file="sample.jpg"
name=${file%.*} # sample
extension=${file#*.} # jpg, prefer ##

# % and %%, similar to # and ##
file="ryan.nus.sg.log"
name=${file%.*} # ryan.nus.sg
name=${file%%.*} # ryan

```
## rename and move files in bulk
```bash

count=1
for img in `find . -type f -iname "*.jpg" -maxdepth 1`
do
    new=image-$count.${img##*.}
    mv $img $new
    let count++
done

rename *.JPG *.jpg
rename 's/ /_/g' *  # replace space by _
rename 'y/A-Z/a-z/' * # upper to lower
```
## spell check and dictionary manipulation
```bash

```
## automate interactive input
```bash
echo -e "1\nhello\n" | ./interactive.sh
./interactive.sh < input.data

# expect
./automate_expect.sh
#Filename: automate_expect.sh
spawn ./interactive .sh # specifies which commands are to be automated
expect "Enter number:"
send "1\n"
expect "Enter name:"
send "hello\n"
expect eof  # end of the command interaction

```
## make commands using parallel processes
```bash
PIDs=()
for file in File1.iso File2.iso
do
md5sum $file &
PIDs+=("$!")
done
wait ${PIDs[@]}
```

# File In File Out

## generate files of any size
```bash
dd if=/dev/zero of=junk.data bs=1M count=1
# bs block size, count - number of blocks
# /dev/zero is a character special device, which infinitely returns the zero byte (\0).
# also used to check the speed of memory operation
```
## itersection and set difference (A-B)
```bash
comm A.txt B.txt
# 1st column - only in A
# 2nd column - only in B
# 3rd column - common lines. each column are delimited by \t

comm A.txt B.txt -1 -2  # remove 1 and 2, so show intersaction
comm A.txt B.txt -3 | sed 's/^\t//'
# s-substitute, to remove every \t
# /^ - start of line marker; // - no char, replacement string
```
## find and delete duplicate file
```bash
# remove_duplicates.sh
# -S : sorted by file size
# --time-style=long-iso : print date in ISO format
# getline : get the first line and dump it
ls -lS --time-style=long-iso | awk 'BEGIN {
    getline; getline;
    name1=$8; size=$5
}
{
    name2=$8;
    if (size==$5) 
    {
        "md5sum "name1 | getline; csum1=$1;
        "md5sum "name2 | getline; csum2=$1;
        if ( csum1==csum2 )
        {
            print name1; print name2
        }
    };
    size=$5; name1=name2;
}' | sort -u > duplicate_files
cat duplicate_files | xargs -I {} md5sum {} | sort | uniq -w 32 |
awk '{ print "^"$2"$" }' | sort -u > duplicate_sample
echo Removing..
comm duplicate_files duplicate_sample -2 -3 | tee /dev/stderr |
xargs rm
echo Removed duplicates files successfully.

```
## file permission, ownership and sticky bit
```bash

# - regular file,d dir，c char device，b block device,l symbolic link,s socket,p pipe

# u – specifies user permissions
# g – specifies group permissions
# o – specifies others permissions
chmod a+x filename # all
chown user.group filename
chmod 777 . -R  # -R apply recursively
```
## make file immutable
```bash
chattr +i file  # cannot remove
chattr -i file  # remove immutable
```
## generate blank files in bulk
```bash
touch filename # create new file or change timestamp
# -a modify access time, -m modification time
touch -d "Jan 20 2018" filename # change date
```
## find symbolic links and their targets
```bash
# create symbolic link
ln -s target sym_link_name

# print symbolic link
ls -l | grep "^l"   # ^l means starting with l
find . -type l -print

readlink link_name
```
## enumerate file type statistics
```bash
# type of file
file filename   # -b to exclude the filename in the output

# Filename: filestat.sh
if [ $# -ne 1 ];
then
echo "Usage is $0 basepath";
exit
fi
path=$1
declare -A statarray;
while read line;
do
ftype=`file -b "$line" | cut -d, -f1`   # use , delimiter and use only 1st field
let statarray["$ftype"]++;
done < <(find $path -type f -print)
echo ============ File types and counts =============
for ftype in "${!statarray[@]}";    # list of array indexes
do
echo $ftype : ${statarray["$ftype"]} 
done

./filetype.sh /home/slynux/programs

while read line;
do something
done < filename
# <(find $path -type f -print) is equivalent to a filename
# the first < is for input redirection and the second < is for converting the subprocess output to a filename
# space there to avoid << operator
# bash 3.x : done <<< "`find $path -type f -print`"

```
## use loopback files
```bash

```
## create ISO file and hybrid ISO
```bash
dd if=/dev/cdrom of=image.iso
mkisofs -V "Label" -o image.iso source_dir/
```
## find the difference between files, patching
```bash
diff -u version1.txt version2.txt > version.patch
patch -p1 version1.txt < version.patch # get version2
patch -p1 version2.txt < version.patch # get version1

diff -Naur directory1 directory2
# -N treat absent file as empty, -a consider all files as txt, -u unified
```
## head and tail
```bash
head -n 4 file  # first 4
head -n -4 file # exclude last 4

tail -n 5 file # last 5
tail -n +6 file # exclude first 5

tail -f growing_file    # follow
tail -f file --pid $PID # tail until PID finish
```
## list only directory
```bash
ls -d */
ls -F | grep "/$"
ls -l | grep "^d"
find . -type d -maxdepth 1 -print

```
## fast command line navigation, pushd and popd
```bash
pushd home/     # /home /
pushd ../lib    # /lib /home /
pushd ../var    # /var /lib /home /
                #   0    1    2   3
pushd +2        # /home / /var /lib 
dirs
popd            # / /var /lib
popd +2         # / /var

cd -        # switch back
```
## count number of lines, words and chars
```bash
wc -l file  # number of line
wc -w file  # number of word
wc -c file
wc file -L  # get length of longest line
```
## print the directory tree
```bash
tree . -P "*.sh"    # highlight the files matching the pattern
tree . -I "*.sh"    # exclude pattern
tree -h # size
tree . -H http://localhost -o out.html
```

# Texting and Driving

## regular expression
```bash
# match all words
# ? zero or one occurrence of the previous expression
( ?[a-zA-Z]+ ?)

# ip
[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}

# ^     start of line
# $     end of line
# .     any one char
# []    any one chars in [chars]
# [^]   any one chars except [chars]
# [-]   any chars within the range [1-5]
# ?     the preceding item must use one or 0 times, colou?r = color or colour
# +     the preceding item must use one or more times, 90+ = 900 or 90000
# *     the preceding item must use 0 or more times, 90* = 90, 900
# ()    treats the terms as one entity, ma(tri)?x = max or matrix
# {n}   the preceding item must use n times, [0-9]{3} = [0-9][0-9][0-9]
# {n,}  specify minimum times that preceding item use, [0-9]{2,} = any 2 digits or longer
# {n,m} [0-9]{2,5} = any number that has 2 digits to 5 digits
# |     alternation, 1 | 2 = 1 or 2
# \     escape, a\.b = a.b

```
## search text inside a file
```bash
grep "pattern" filename --color=auto
grep -E "[a-z]+" filename # -E extended reg ex
# -o ouput only match portion
# -v print all the lines except the matched line
# -c count number of matched line
# -e count number of matched item
# -i ignore case

grep "pattern" -n filename # print line number

echo gnu is not unix | grep -b -o "not" # 7:not
grep -l linux file1 file2 # list which file contain, -L for non-matching
grep "pattern" . -R -n  # common

# pattern file
grep -f pat_file

grep "main()" . -r --include *.{c,cpp}  #  search *.c and *.ccp
grep "main()" . -r --exclude *.{c,cpp}  #  not search *.c and *.ccp
grep "main()" . -r --exclude-dir ./tmp  # exclude dir

# xargs
grep "test" file* -lZ | xargs -0 rm # -Z and -0
# -q : quite, return status without showing

# Printing lines before and after text matches
# print three lines after a match, -A; -b lines before; -c both
seq 10 | grep 5 -A 3    #5\n6\n7\n8

```
## cut a file by column
```bash
cut -f 2,3 filename # show 2nd and 3rd column, --complement for reverse
# -d ":" to indicate delimiter

cut -c 1-5 filename    # 1st to 5th chars
cut -c -5 filename    # 1st 5 chars
cut -c 2- filename    # from 2nd to the end

cut filename -c 1-3,6-9 --output-delimiter ","  # abc,fghi

```
## sed for text replacement
```bash
sed 's/pattern/replace_string/' file    # -i save changes
sed 's/pattern/replace_string/' file > newfile

sed 's/pattern/replace_string/g' file   # substitute every occurrence
echo thisthisthisthis | sed 's/this/THIS/2g' # thisTHISTHISTHIS
# / can be | or :

# remove blank line
sed '/^$/d' filename    # d - delete

sed -i .bak 's/abc/def/' filename # change original file as .bak

# Matched string notation (&)
echo this is an example | sed 's/\w\+/[&]/g' # \w\+ matches every word. & refer to each of words
# [this] [is] [an] [example]

# Substring match notation (\1)
echo seven EIGHT | sed 's/\([a-z]\+\) \([A-Z]\+\)/\2 \1/'
# \([a-z]\+\) match 1st word and \1 refer to 

# multiple action
$ echo abc | sed -e 's/a/A/' -e 's/c/C/'    #AbC
```
## awk for text processing
```bash
# for each input, {statement} will execute.
awk 'BEGIN { statements } { statements } END { end statements }'

# NR : number of record. each input line as one record
# NF : number of field. delimiter by space
# $0 : text content of current line
# $1, $2.. 1st field, 2nd field

# like java, string is within "". 
awk '{ print "Line no:"NR",No of fields:"NF, "$0="$0, "$1="$1}'

awk '{ print $3,$2 }' file
awk 'END{ print NR }' file  # get number of lines

awk '{ print v1,v2 }' v1=$var1 v2=$var2 filename

# getline var

awk 'NR < 5' # first four lines
awk 'NR==1,NR==4' #First four lines
awk '/linux/' # Lines containing the pattern
awk '!/linux/' # Lines not containing the pattern
awk -F : '{ print $NF }' /etc/passwd   # -F delimiter

# awk built in function
# length(string): string length.
# index(string, search_string): position at which search_string is found
# split(string, array, delimiter): stores strings list generated by delimiter in the array
# substr(string, start-position, end-position): 
# sub(regex, replacement_str, string): replaces the first occurring match
# gsub(regex, replacment_str, string): replaces every regular expression match.
# match(regex, string): non-zero output if a match is found, otherwise zero. 
# with match(). RSTART contains the position the match starts. RLENGTH contains match length
```
## find frequency of words
```bash

```
## compress and decompress javascript
```bash
cat sample.js | \
tr -d '\n\t' | \    # Removes the \n and \t characters
tr -s ' ' | \       # squeeze repeating space
sed 's:/\*.*\*/::g' | \ # Removes comments, use : as delimiter instead of /, .* match all text
sed 's/ \?\([{}();,:]\) \?/\1/g'    # Removes all the spaces before and after the characters. 

# decompression
# s/;/;\n/g replaces ; with ;\n
# s/{/{\n\n/g replaces { with {\n\n
# s/}/\n\n}/g replaces } with \n\n}
```
## merge files as columns
```bash
paste file1 file2 -d ","
# 1, xxx
# 2, xxxxx
```
## print the nth word or column
```bash
# print the fifth column
awk '{ print $5 }' filename

ls -l | awk '{ print $1 " : " $8 }' # get 1st and 8th column
```
## print text between line number or pattern
```bash
awk 'NR==M, NR==N' filename
awk '/start_pattern/, /end _pattern/' filename
```
## print line reversely
```bash
seq 9 | \
awk '{ lifo[NR]=$0 }
END{ for(lno=NR;lno>-1;lno--){ print lifo[lno]; }
}'
```
## parse email and URL
```bash
egrep -o '[A-Za-z0-9._]+@[A-Za-z0-9.]+\.[a-zA-Z]{2,4}' filename
egrep -o "http://[a-zA-Z0-9.]+\.[a-zA-Z]{2,3}" filename
```
## remove a sentence containing a word
```bash
sed 's/ [^.]*mobile phones[^.]*\.//g' sentence.txt
```
## replace a pattern with text in one directory
```bash
find . -name *.cpp -print0 | xargs -I{} -0 sed -i 's/Copyright/Copyleft/g' {}
find . -name *.cpp -exec sed -i 's/Copyright/Copyleft/g' \{\} \;
```
## text slicing and parameter operations
```bash
var="This is a line of text"
echo ${var/line/REPLACED_text}

string="abcdefghijklmnopqrstuvwxyz"
echo ${string:4}    # efghijklmnopqrstuvwxyz
echo ${string:4:8}  # efghijkl
echo ${string:(-1)} # z
$ echo ${string:(-2):2} # yz
```

# Tangled Web
## download from web page
```bash
wget URL
wget ftp_link -O dl_file.img -o log
wget -t 5 URL   # try 5 times
wget -t 0 URL   # try infinitely
wget --limit-rate 20k URL   # limit speed
wget -Q 100m URL    # --quota, total download size
wget -c URL # resume downloading

# download the complete website by recursively collecting all the URL
wget --mirror --convert-links exampledomain.com
wget -r -N -l -k DEPTH URL
# -l : depth, along with -r. -N enable time stamping. 
# -k or --convert-links : convert the links to other pages

wget --user username --password pass URL
wget --user username --ask-password URL
```
## download as plain text
```bash
lynx URL -dump > webpage_as_text.txt
```
## cURL
```bash
curl URL -o index.html --progress   # show progress
curl URL/file -C offset # Continuing and resuming

# Setting the referer string with cURL
# click link_1 in page_a, we access to page_b. Without link_1, we are not able to access page_b.
curl --referer link_1 page_b_URL 

# Cookies with cURL
curl URL --cookie "user=ryan;pass=hi"
curl URL --cookie-jar cookie_file   # specify a file to which the cookies encountered are to be stored

curl URL --limit-rate 20k
curl URL --max-filesize bytes   
# return a non-zero exit code if the file size exceeds. zero if it succeeds

curl -u user:pass URL
curl -u user URL

# -I or -head : dump only the HTTP headers, without downloading the remote file.
curl -I URL
```
## access Gmail
```bash
#Desc: Fetch gmail tool
username='PUT_USERNAME_HERE'
password='PUT_PASSWORD_HERE'
SHOW_COUNT=5 # No of recent unread mails to be shown
echo
curl -u $username:$password --silent "https://mail.google.com/mail/feed/atom" | \
tr -d '\n' | sed 's:</entry>:\n:g' |\
sed -n 's/.*<title>\(.*\)<\/title.*<author><name>\([^<]*\)<\/name><email>\([^<]*\).*/From: \2 [\3] \nSubject: \1\n/p' | \
head -n $(( $SHOW_COUNT * 3 ))
# tr -d '\n' removes the newline character
# sed 's:</entry>:\n:g' replaces every </entry> element with a newline
```
## parsing data from website
```bash

```
## image crawler and downloader
```bash
#Filename: img_downloader.sh
if [ $# -ne 3 ];
then
    echo "Usage: $0 URL -d DIRECTORY"
    exit -1
fi

# shift is used to shift arguments to the left
# $1 will take the next argument value
for i in {1..4}
do
    case $1 in
        -d) shift; directory=$1; shift ;;
        *) url=${url:-$1}; shift;;
    esac
done
mkdir -p $directory;
baseurl=$(echo $url | egrep -o "https?://[a-z.]+")
echo Downloading $url
curl -s $url | egrep -o "<img src=[^>]*>" | sed 's/<img src=\"\([^"]*\).*/\1/g' >/tmp/$$.list
sed -i "s|^/|$baseurl/|" /tmp/$$.list
cd $directory;

while read filename;
do
    echo Downloading $filename
    curl -s -O "$filename" --silent
done < /tmp/$$.list
```
## web photo album generator
```bash
#Description: Create a photo album using images in current directory
echo "Creating album.."
mkdir -p thumbs
cat <<EOF1 > index.html
<html>
<head>
<style>
    body
    {
        width:470px;
        margin:auto;
        border: 1px dashed grey;
        padding:10px;
    }
    img
    {
        margin:5px;
        border: 1px solid black;
    }
</style>
</head>
<body>
<center><h1> #Album title </h1></center>
<p>
EOF1

for img in *.jpg;
do
    convert "$img" -resize "100x" "thumbs/$img"
    echo "<a href=\"$img\" ><img src=\"thumbs/$img\" title=\"$img\" /></a>" >> index.html
done

cat <<EOF2 >> index.html
</p>
</body>
</html>
EOF2
echo Album generated to index.html
```
## twitter command-line client
```bash

```
## create a 'define' utility by using web backend
1. http://www.dictionaryapi.com/register/index.htm
2. get key
```bash
#Desc: A script to fetch definitions from dictionaryapi.com
apikey=YOUR_API_KEY_HERE # fd750d43-a3b8-4f47-8055-e8db4ebfeeed
if [ $# -ne 2 ];
then
echo -e "Usage: $0 WORD NUMBER"
exit -1;
fi
curl --silent http://www.dictionaryapi.com/api/v1/references/learners/xml/$1?key=$apikey | \
grep -o \<dt\>.*\</dt\> | \
sed 's$</*[a-z]*>$$g' | \
head -n $2 | nl
```
## find broken links in a website
```bash
#Desc: Find broken links in a website
if [ $# -ne 1 ];
then
    echo -e "$Usage: $0 URL\n"
    exit 1;
fi
echo Broken links:
mkdir /tmp/$$.lynx
cd /tmp/$$.lynx
lynx -traversal $1 > /dev/null
count=0;
sort -u reject.dat > links.txt
while read link;
do
    output=`curl -I $link -s | grep "HTTP/.*OK"`;
    if [[ -z $output ]];
    then
        echo $link;
        let count++
    fi
done < links.txt
[ $count -eq 0 ] && echo No broken links found.
```
## track changes to a website
```bash
#Desc: Script to track changes to webpage
if [ $# -ne 1 ];
then
    echo -e "$Usage: $0 URL\n"
    exit 1;
fi
first_time=0
# Not first time
if [ ! -e "last.html" ];
then
    first_time=1
    # Set it is first time run
fi
curl --silent $1 -o recent.html
if [ $first_time -ne 1 ];
then
    changes=$(diff -u last.html recent.html)
    if [ -n "$changes" ];
    then
        echo -e "Changes:\n"
        echo "$changes"
    else
    echo -e "\nWebsite has no changes"
    fi
else
    echo "[First run] Archiving.."
fi
cp recent.html last.html
```
## post to web page and read response
```bash
# POST and read the HTML response
curl URL -d "host=hostname&user=username"   # -d is the argument used for posting
wget php_URL --post-data "host=hostname&user=username" -O output.html
```

# Backup Plan

## Archive with tar
```bash
tar -cf output.tar [SOURCES]    # -c create file, -f specify filename
tar -tf archive.tar # list files in an archive
tar -tvf archive.tar    # -v verbose, print more details

tar -rvf original.tar new_file  # -r append a file into an archive

tar -xf archive.tar # -x for extract
tar -xf archive.tar -C /path/to/extract # -C to indicate where the files
tar -xvf file.tar file1 file4   # only extract file1 and file4

# '-' make archive as output
tar cvf - files/ | ssh a@A "tar xv -C Documents/"

# merge multiple tar files with the -A option
tar -Af file.tar file_be_merged.tar

# -u : append updated files according to timestamp.
tar -uf archive.tar filea   # when extracting, newer version will be picked up

# Comparing files in the archive and file system
tar -df archive.tar # -d to print difference

# Deleting files from the archive
tar -f archive.tar --delete file1 file2

# -a : compress by looking at the extension
tar -acvf archive.tar.gz filea fileb filec

# Exclude a set of files from archiving
tar -cf arch.tar * --exclude "*.txt"
tar -cf arch.tar * -X list  # list: file1 file2

# Excluding version control directories
tar --exclude-vcs -czvvf archive.tar.gz eye_of_gnome_svn
tar -cf arc.tar * --exclude "*.txt" --totals
```
## archive with cpio
```bash
# store files with attributes such as permissions, ownership
echo file1 file2 file3 | cpio -ov > archive.cpio
cpio -it < archive.cpio # list files in a cpio archive
cpio -id < archive.cpio # extract files from the cpio archive
# -o specifies the output
# -v is used for printing a list of files archived
# -i is for specifying the input
# -t is for listing
# -d stands for extracting

```
## compress data with gzip
```bash
gzip filename
gunzip filename.gz
gzip -l test.txt.gz # list out the properties of a compressed file
cat file | gzip -c > file.gz    # -c option is used to specify output to stdout

# zip multiple files
FILE_LIST="file1 file2 file3 file4 file5"
for f in $FILE_LIST;
do
tar -rvf archive.tar $f
done
gzip archive.tar

# extract a gzipped tarball
tar -xavvf archive.tar.gz -C extract_directory

# zcat - reading gzipped files without extracting
zcat test.gz

# Compression ratio
gzip -5 test.img    # 1 is the lowest, but fastest

# bzip2 offers more effective compression while taking more time
bzip2 filename
bunzip2 filename.bz2

tar -xjvf archive.tar.bz2   # -j denotes that the archive is bzip2

```
## archive and compress with zip
```bash
zip archive_name.zip [SOURCE FILES/DIRS]
zip -r archive.zip folder1 folder2  # Archive directories and files recursively
unzip file.zip  # without removing filename.zip
zip file.zip -u newfile # update files in the archive with newer files
zip -d arc.zip file.txt # Delete a file from a zipped archive
unzip -l archive.zip    # list the files in an archive
```
## faster archive with pbzip2
```bash
# pbzip2 can use multiple cores, hence decreasing overall time
pbzip2 myfile.tar
tar cf myfile.tar.bz2 --use-compress-prog=pbzip2 dir_to_compress
pbzip2 -dc myfile.tar.bz2 | tar x   # Extracting a pbzip2
```
## create filesystem with compression
```bash
# squashfs 
```
## backup snapshots with rsync
```bash

```
## version control-based backup with Git
```bash

## u1 as remote machine; 192.168.1.135
# Set up and initiate the remote backup directory
mkdir -p ~/git_repo/demo.git
cd ~/git_repo/demo.git
git init --bare

## base as source host machine
git config --global user.name "ryan"
git config --global user.email luoxu2011@gmail.com
# source documents in ~/git_demo 
cd ~/git_demo
git init
git remote add origin ryan@192.168.1.135:~/git_repo/demo.git
git add .
git commit -m "data1"
git push origin master

git log
# revert back to any previous state or version
git checkout COMMIT_ID
# make this revert permanent
git commit -am "Restore @ $(date) commit ID:xxxxxx"

git clone ryan@192.168.1.135:~/git_repo/demo.git

```
## create entire disk images using fsarciver
```bash

```

# Old Boy Network

## Setup the network
```bash
# manually set the IP address for a network interface
sudo ifconfig wlan0 xxx.xxx.xx.xx netmask 255.255.252.0

# Printing the list of network interfaces
ifconfig | cut -c-10 | tr -d ' ' | tr -s '\n'
# first 10 characters is reserved for network interfaces
# take the first 10 columns, remove space, squeeze newline

# Displaying IP addresses
ifconfig wlan0 | egrep -o "inet addr:[^ ]*" | grep -o "[0-9.]*"

# spoof the hardware address at the software level
ifconfig eth0 hw ether 00:1c:bf:87:25:d5

cat /etc/resolv.conf    # nameserver 8.8.8.8
host google.com # list out all of the IP attached to the domain name
nslookup google.com # 

echo 192.168.0.9 backupserver >> /etc/hosts
ping backupserver # get 192.168.0.9

# Showing routing table information
route -n

# set a default gateway
route add default gw IP_ADDRESS INTERFACE_NAME

```
## Ping
```bash
ping hostname # Round Trip Time (RTT)
ping 192.168.0.1 -c 2 # Limiting the number of packets to be sent

# Return status of the ping command
ping domain -c2
if [ $? -eq 0 ];
then
echo Successful ;
else
echo Failure
fi

# traceroute displays the address of all intermediate gateways
traceroute google.com
```

## list all the machine alive
```bash
# check alive host
for ip in 192.168.0.{1..255} ;
do
    ping $ip -c 2 &> /dev/null ;  # &> /dev/null : redirect both stderr and stdout
    if [ $? -eq 0 ];
    then
        echo $ip is alive
    fi
done

# Parallel pings : enclose the loop body in ( )&
for ip in 192.168.0.{1..255} ;
do
    (
        ping $ip -c2 &> /dev/null ;
        if [ $? -eq 0 ];
        then
            echo $ip is alive
        fi
    )&
done
wait # waits for the time until all the child ( ) subshell processes complete
```
## run command on a remote host with SSH
```bash
# By default, the SSH server runs at port 22
ssh user@locahost -p 422

COMMANDS="command1; command2; command3"
$ ssh user@hostname "$COMMANDS"

# collects the uptime of a list of remote hosts
#Description: Uptime monitor
IP_LIST="192.168.0.1 192.168.0.5 192.168.0.9"
USER="test"
for IP in $IP_LIST;
do
    utime=$(ssh ${USER}@${IP} uptime | awk '{ print $3 }' )
    echo $IP uptime: $utime
done

# Redirecting data into stdin of remote host
echo 'text' | ssh user@remote_host 'echo'
ssh user@remote_host 'echo' < file

# Running graphical commands on a remote machine

```
## transfer files through the network
```bash
# FTP can only be used if the FTP server is installed on the remote machine.
# FTP usually runs on port 21.
lftp username@ftphost

# To change the directory of a local machine, use lcd

# download a file
lftp username@ftphost:~> get filename

# To upload a file from the current directory
lftp username@ftphost:~> put filename

# An lftp session can be terminated by using the quit

# Automated FTP transfer
# -i option of ftp turns off the interactive session
# -n option tells ftp to not attempt automatically logging in and use the username and password we supply it.
HOST='domain.com'
USER='foo'
PASSWD='password'
ftp -i -n $HOST <<EOF   
user ${USER} ${PASSWD}
binary
cd /home/slynux
put testfile.jpg
get serverfile.jpg
quit
EOF

# SFTP requires an OpenSSH server to be installed and running
sftp user@domainname
sftp -oPort=422 user@domainname # in case port no is not 22

scp SOURCE DESTINATION
scp filename user@remotehost:/home/path # -r Recursive copy

```
## connect to a wireless network
```bash

```
## passwordless SSH
```bash
ssh-keygen -t rsa
ssh USER@REMOTE_HOST "cat >> ~/.ssh/authorized_keys" < ~/.ssh/id_rsa.pub
```
## port forwarding using SSH
```bash

```
## mounte a remote drive at a local mount point
```bash

```
## network traffic and port analysis
```bash
# list all opened ports on the system along with the details
lsof -i

# In order to list out the opened ports from the current machine
lsof -i | grep ":[0-9]\+->" -o | grep "[0-9]\+" -o | sort | uniq

# netstat -tnp to list opened port and services
netstat -tnp

```
## create arbitrary sockets
```bash
# reate network sockets to do data transfer over a TCP/IP network
# create a listening socket on port 1234 on the local machine
nc -l 1234  # netcat

# Connect to the socket
nc HOST 1234 # replace HOST with localhost, or the IP address or hostname

ryan@base:~$ nc -l 12345
ryan@u1:~$ nc base 12345
# type hi in u1, hi will appear in base as well


# Quickly copying files over the network
nc -l 1234 > destination_filename   # on receiving machine
nc HOST 1234 < source_filename  # on sender machine

```
## share an internet connection
```bash
# you don't have a router and you need to share the Internet?

```
## basic firewall using iptables
```bash
# Block traffic to a specific IP address
iptables -A OUTPUT -d 8.8.8.8 -j DROP

# Block traffic to a specific port
iptables -A OUTPUT -p tcp -dport 21 -j DROP
# -A which instructs iptables to append a new rule to the chain
# OUTPUT chain which runs on all the outgoing traffic
# -j to instruct iptables to DROP the packet
# -p parameter to specify that this rule should match only TCP on the port specified with -dport which block all the outbound FTP traffic


# clear the changes made to the iptables chains
iptables --flush
```

# Put on the Monitor's Cap

## Monitor disk usage
```bash
du -a DIRECTORY # -a for all files recursively
du -h FILENAME

# Displaying the grand total sum of disk usage
du -c FILENAME1 FILENAME2..
du -c DIRECTORY
# -s (summarize), which will print only the grand total

# Excluding files from the disk usage calculation
du --exclude "*.txt" FILES
du --exclude-from EXCLUDE.txt DIRECTORY
du --max-depth 2 DIRECTORY

# Finding the 10 largest size files from a given directory
du -ak SOURCE_DIR | sort -nrk 1 | head
# -a makes du traverse the SOURCE_DIR and calculates the size of all files
# sort is used to perform a numerical sort with column 1 and reverse it

# find only the largest files and not directories
find . -type f -exec du -k {} \; | sort -nrk 1 | head

```

## execution time for a command
```bash
time COMMAND
/usr/bin/time -a -o output.txt COMMAND
# append the time statistics to a file without overwriting, use the -a

# Real time: %e
# User: %U
# sys: %S
/usr/bin/time -f "Time: %U" -a -o timing.log COMMAND  # formatted output
/usr/bin/time -f "Time: %U" COMMAND> command_output.txt 2>time.log

# %C Name and command-line arguments of the command being timed.
# %D Average size of the process unshared data area, in kilobytes.
# %E Elapsed real (wall clock) time used by the process in [hours:]minutes:seconds.
# %x Exit status of the command.
# %k Number of signals delivered to the process.
# %W Number of times the process was swapped out of the main memory.
# %Z System page size in bytes. This is a per-system constant, but varies between systems
# %P Percentage of the CPU that this job got. This is just user + system times divided by the total running time. It also prints a percentage sign.
# %K Average total (data + stack + text) memory usage of the process, in Kilobytes.
# %w Number of times that the program was context-switched voluntarily, for instance while waiting for an I/O operation to complete.
# %c Number of times the process was context-switched involuntarily (because the time slice expired).
```

## logged in users, boot logs and boot failure
```bash
who
w # more detailed information about the logged in users
users # list only the usernames of the users
users | tr ' ' '\n' | sort | uniq   # find unique users
uptime # how long the system has been powered on
# 21:44:33 up 3:17, 8 users, load average: 0.09, 0.14, 0.09
uptime | grep -Po '\d{2}\:\d{2}\:\d{2}' # xtract only three two-digit numbers separated by colons.

# get information about previous boot and user logged sessions
last
last -f /var/log/wtmp
last USER # about login sessions for a single user
last reboot # Get information about reboot sessions
lastb  # about failed user login sessions
```

## top 10 cpu consuming processes in an hour
```bash
# monitoring and calculating CPU usages in one hour
SECS=3600
UNIT_TIME=60
#Change the SECS to total seconds for which monitoring is to be performed.
#UNIT_TIME is the interval in seconds between each sampling
STEPS=$(( $SECS / $UNIT_TIME ))
echo Watching CPU usage... ;
for((i=0;i<STEPS;i++))
do
    ps -eocomm,pcpu | tail -n +2 >> /tmp/cpu_usage.$$
    sleep $UNIT_TIME
done
echo
echo CPU eaters :
cat /tmp/cpu_usage.$$ | \
awk '{ process[$1]+=$2; }END{for(i in process){printf("%-20s %s\n",i, process[i]) ;}}' \
| sort -nrk 2 | head
rm /tmp/cpu_usage.$$
#Remove the temporary log file
```

## mmonitor command output with watch
```bash
# monitor the output of a command
watch COMMAND
watch 'ls -l | grep "^d"' # will update the output at a default interval of two seconds
watch -n 5 'ls -l' # at regular intervals of 5 seconds
watch -d 'COMMAND' # Difference highlighting
```

## log access to files and directories
```bash

```

## logfile management with logrotate
```bash
# logrotate has the configuration directory at /etc/logrotate.d.
# customerized config file: /etc/logrotate.d/program
/var/log/program.log {  # logfile path
missingok   # Ignore if the logfile is missing and return without rotating the log.
notifempty  # Only rotate the log if the source logfile is not empty
size 30k    # Limit the size of the logfile
compress    # Enable compression with gzip for older logs.
weekly      # Specify the interval at which the rotation is to be performed
rotate 5    # It is the number of older copies of logfile archives to be kept
create 0600 root root   # Specify the mode, user, and the group of the logfile archive
}
```

## logging with syslog
```bash
#/var/log/boot.log      Boot log information.
#/var/log/httpd         Apache web server log.
#/var/log/messages      Post boot kernel information.
#/var/log/auth.log      User authentication log.
#/var/log/dmesg         System boot up messages.
#/var/log/mail.log      Mail server log.
#/var/log/Xorg.0.log    X Server log.

#logger LOG_MESSAGE
logger This is a test log line
tail -n 1 /var/log/messages
# Sep 29 07:47:44 slynux-laptop slynux: This is a test log line
# it logs to /var/log/messages by default

# with a specified tag
logger -t TAG This is a message
# Sep 29 07:48:42 slynux-laptop TAG: This is a message

# You can see the tag strings and associated logfiles from the configuration files located in the /etc/rsyslog.d/ directory.

```

## monitor user login to find intruder
```bash
#!/bin/bash
#Filename: intruder_detect.sh
#Description: Intruder reporting tool with auth.log input
AUTHLOG=/var/log/auth.log
if [[ -n $1 ]];
then
AUTHLOG=$1
echo Using Log file : $AUTHLOG
fi
LOG=/tmp/valid.$$.log
grep -v "invalid" $AUTHLOG > $LOG
users=$(grep "Failed password" $LOG | awk '{ print $(NF-5) }' | sort |
uniq)
printf "%-5s|%-10s|%-10s|%-13s|%-33s|%s\n" "Sr#" "User" "Attempts" "IP
address" "Host_Mapping" "Time range"
ucount=0;
ip_list="$(egrep -o "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" $LOG | sort |
uniq)"
for ip in $ip_list;
do
grep $ip $LOG > /tmp/temp.$$.log
for user in $users;
do
grep $user /tmp/temp.$$.log> /tmp/$$.log
cut -c-16 /tmp/$$.log > $$.time
tstart=$(head -1 $$.time);
start=$(date -d "$tstart" "+%s");
tend=$(tail -1 $$.time);
end=$(date -d "$tend" "+%s")
limit=$(( $end - $start ))
if [ $limit -gt 120 ];
then
let ucount++;
IP=$(egrep -o "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" /tmp/$$.log |
head -1 );
TIME_RANGE="$tstart-->$tend"
ATTEMPTS=$(cat /tmp/$$.log|wc -l);
HOST=$(host $IP | awk '{ print $NF }' )
printf "%-5s|%-10s|%-10s|%-10s|%-33s|%-s\n" "$ucount" "$user"
"$ATTEMPTS" "$IP" "$HOST" "$TIME_RANGE";
fi
done
done
rm /tmp/valid.$$.log /tmp/$$.log $$.time /tmp/temp.$$.log 2> /dev/null
```

## remote disk usage health monitor
1. set up a common user account on all the remote machines
2. configure auto-login with SSH
```bash
#!/bin/bash
#Filename: disklog.sh
#Description: Monitor disk usage health for remote systems
logfile="diskusage.log"
if [[ -n $1 ]]
then
    logfile=$1
fi
if [ ! -e $logfile ]
then
    printf "%-8s %-14s %-9s %-8s %-6s %-6s %-6s %s\n" "Date" "IP
    address" "Device" "Capacity" "Used" "Free" "Percent" "Status" > $logfile
fi
IP_LIST="127.0.0.1 0.0.0.0"
#provide the list of remote machine IP addresses
(
for ip in $IP_LIST;
do
#slynux is the username, change as necessary
ssh slynux@$ip 'df -H' | grep ^/dev/ > /tmp/$$.df
while read line;
do
cur_date=$(date +%D)
printf "%-8s %-14s " $cur_date $ip
echo $line | awk '{ printf("%-9s %-8s %-6s %-6s
%-8s",$1,$2,$3,$4,$5); }'
pusg=$(echo $line | egrep -o "[0-9]+%")
pusg=${pusg/\%/};
if [ $pusg -lt 80 ];
then
echo SAFE
else
echo ALERT
fi
done< /tmp/$$.df
done
) >> $logfile

# We can use the cron utility to run the script at regular intervals
# every day at 10 a.m., write the following entry in the crontab
00 10 * * * /home/path/disklog.sh /home/user/diskusg.log

```

## measure and optimize power usage
```bash
# The log data is stored in the /var/log/wtmp file

#!/bin/bash
#Filename: active_users.sh
#Description: Reporting tool to find out active users
log=/var/log/wtmp
if [[ -n $1 ]];
then
log=$1
fi
printf "%-4s %-10s %-10s %-6s %-8s\n" "Rank" "User" "Start" "Logins"
"Usage hours"
last -f $log | head -n -2 > /tmp/ulog.$$
cat /tmp/ulog.$$ | cut -d' ' -f1 | sort | uniq> /tmp/users.$$
(
while read user;
do
grep ^$user /tmp/ulog.$$ > /tmp/user.$$
minutes=0
while read t
do
s=$(echo $t | awk -F: '{ print ($1 * 60) + $2 }')
let minutes=minutes+s
done< <(cat /tmp/user.$$ | awk '{ print $NF }' | tr -d ')(')
firstlog=$(tail -n 1 /tmp/user.$$ | awk '{ print $5,$6 }')
nlogins=$(cat /tmp/user.$$ | wc -l)
hours=$(echo "$minutes / 60.0" | bc)
printf "%-10s %-10s %-6s %-8s\n" $user "$firstlog" $nlogins $hours
done< /tmp/users.$$
) | sort -nrk 4 | awk '{ printf("%-4s %s\n", NR, $0) }'
rm /tmp/users.$$ /tmp/user.$$ /tmp/ulog.$$
```

## Measuring and optimizing power usage
```bash
powertop
powertop --html # generating HTML reports
```

## monitor disk activity
```bash
iotop -o    # show only those processes which are doing active I/O
iotop -b -n 2 # print the statistics two times and then exit
iotop -p PID # Monitor a specific process
```

## check disk and filesystem for errors
```bash
# check for errors on a partition or filesystem
fsck /dev/sdb3

# check all the filesystems configured in /etc/fstab
fsck -A

# Instruct fsck to automatically attempt fixing errors
fsck -a /dev/sda2

# simulate the actions
fsck -AN
```

# Administration Calls
## Gathering information about processes
```bash
ps -f # -f (stands for full) 
# other than the ones attached to the current terminal, -e (every) option

# display the required columns of output, -o
ps -eo comm,pcpu | head
#pcpu Percentage of CPU
#pid Process ID
#ppid Parent Process ID
#pmem Percentage of memory
#comm Executable filename
#cmd Simple command
#user The user who started the process
#nice The priority (niceness)
#time Cumulative CPU time
#etime Elapsed time since the process started
#tty The associated TTY device
#euid The effective user
#stat Process state

# display several parameters along with the top CPU consuming processes
top

# Sorting the ps output
ps -eo comm,pcpu --sort -pcpu | head

# Finding the process ID when given command names
ps -C COMMAND_NAME
ps -C COMMAND_NAME -o pid= # lists the process IDs
pgrep COMMAND
pgrep bash -d ":" # define delimiter rather than using a newline
pgrep -u root,slynux COMMAND # Specify a list of owners of the user for the matching processes
pgrep -c COMMAND # Return the count of matching processes

# Specify an effective users' list by using -u EUSER1, EUSER2,
# Specify a real users' list by using -U RUSER1, RUSER2
ps -u root -U root -o user,pcpu

# TTY filter for ps
ps -t pts/0,pts/1

# Information about process threads
ps -eLf
# -L : show information about threads. 
# NLWP is the thread count for a process and NLP is the thread ID for each entry
ps -eLf --sort -nlwp | head

# Showing environment variables for a process
ps -eo cmd e

# if /usr/bin/windowapp did not work
ps -C windowapp -eo cmd e   # find out the env variable, and prefix to the cmd
DISPLAY=:0 /usr/bin/windowapp

which # find the location of a command
which ls # /bin/ls
whereis ls
whatis ls # one-line description of the command

# Add path
export PATH=$PATH:/home/ryan/bin

file FILENAME # return file type

# specifies the average of the total number of runnable processes
uptime

```

## Killing processes and send or respond to signals
```bash
kill -l # print signal numbers and corresponding signal names

# specify a signal to be sent to a process via the kill
kill -s SIGNAL PID
kill -s SIGKILL PROCESS_ID # same as : kill -9 PROCESS_ID

killall -u USERNAME process_name
pkill process_name

# trap is a command used to assign signal handler to signals in a script
trap 'signal_handler_function_name' SIGNAL LIST

#/bin/bash
#Filename: sighandle.sh
#Description: Signal handler
function handler()
{
    echo Hey, received signal : SIGINT
}
echo My process ID is $$
trap 'handler' SIGINT
#handler is the name of the signal handler function for SIGINT signal
while true;
do
    sleep 1
done

```

## Sending messages to user terminals
```bash
# broadcast a message to all users and all logged in terminals
cat message.txt | wall


```

## Gathering system information
```bash
# print the hostname of the current system
hostname
uname -n
uname -a
uname -r # kernel release
uname -m # machine type

cat /proc/cpuinfo # print details about the CPU
cat /proc/meminfo # memory info

```

## Using /proc to gather information
```bash
# /proc/4295/environ will display all the environment variables
```

## Scheduling with cron
```bash
02 * * * * /home/slynux/test.sh # execute test.sh at 2nd mintues of all hours
00 5,6,7 * * /home/slynux/test.sh #  fifth, sixth, and seventh hours
00 */12 * * 0 /home/slynux/script.sh  # every hour on Sundays
00 02 * * * /sbin/shutdown -h # 2 A.M. everyday

# -e option to crontab to start editing the cron table
crontab -e

crontab<<EOF
02 * * * * /home/slynux/script.sh
EOF

# Minute (0 - 59)   5,10 or */5
# Hour (0 - 23)
# Day (1 - 31)
# Month (1 - 12)
# Weekday (0 - 6)
# COMMAND (the script

# Specifying environment variables
crontab<<EOF
http_proxy=http://192.168.0.3:3128
00 * * * * /home/slynux/download.sh
EOF

# Running commands at system start up/boot
@reboot command

crontab -l # list existed job
crontab -r # remove cron table for current user
crontab -u ryan -r # need root
```

## Writing and reading the MySQL database from bash
```bash

```

## User administration scripts
```bash
useradd USER -p PASSWORD
passwd USER
addgroup USER GROUP

```

## Bulk image resizing and format conversion
```bash

```

## Taking screenshots from the terminal
```bash

```

## Managing multiple terminals from one
```bash
# using a utility called GNU screen

```

-- END --
