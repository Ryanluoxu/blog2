---
title: Hanoi Tower 汉诺塔 - Python 示例
date: 2020-06-04 20:12:36
categories: Python
tags: [Python, algorithm, hanoi]
---

最近看的一个教程里，提到了汉诺塔的问题，参考别人的代码自己用python实现了一番。

## 4层的效果是这样：
```
A:[4, 3, 2, 1]
B:[]
C:[]
start moving:
move 1 from A to B.
move 2 from A to C.
move 1 from B to C.
move 3 from A to B.
move 1 from C to A.
move 2 from C to B.
move 1 from A to B.
move 4 from A to C.
move 1 from B to C.
move 2 from B to A.
move 1 from C to A.
move 3 from B to C.
move 1 from A to B.
move 2 from A to C.
move 1 from B to C.
A:[]
B:[]
C:[4, 3, 2, 1]
```

<!--more-->

## 核心的代码其实只需要：
```Python
def move(n,a,b,c):
    if n==1:
        c.append(a.pop())
        return
    move(n-1,a,c,b)
    c.append(a.pop())
    move(n-1,b,a,c)
move(3,[3,2,1],[],[])
```

## 完整代码：
为了更直观一些，定义了 Stick 类。
```Python
# ABC三根柱子，A上有N个盘子，如何才能将盘子移到C柱子上，每次只能移动一个，并且大盘总是在小盘下面。
class Stick:
    def __init__(self,name,list):
        self.name=name
        self.list=list
    def pop(self):
        return self.list.pop()
    def append(self, element):
        self.list.append(element)
    def __str__(self):
        return self.name+':'+str(self.list)

def move(n,a,b,c):
    # 1. 将 a 的 n-1 个放到 b
    # 2. 将 a 的 n 个放到 c
    # 3. 将 b 的 n-1 个放到 c
    if n==1:
        move_action(a,c) # c.append(a.pop())
        return
    move(n-1,a,c,b)
    move_action(a,c) # c.append(a.pop())
    move(n-1,b,a,c)

def move_action(source, target):
    size = len(source.list)
    print('move {0} from {1} to {2}.'.format(source.list[size-1],source.name,target.name))
    target.append(source.pop())

a=Stick('A',[4,3,2,1])
b=Stick('B',[])
c=Stick('C',[])
print(a)
print(b)
print(c)

print('start moving:')
n = len(a.list)
move(n,a,b,c)
print(a)
print(b)
print(c)
```

## 5层的结果：
```
A:[5, 4, 3, 2, 1]
B:[]
C:[]
start moving:
move 1 from A to C.
move 2 from A to B.
move 1 from C to B.
move 3 from A to C.
move 1 from B to A.
move 2 from B to C.
move 1 from A to C.
move 4 from A to B.
move 1 from C to B.
move 2 from C to A.
move 1 from B to A.
move 3 from C to B.
move 1 from A to C.
move 2 from A to B.
move 1 from C to B.
move 5 from A to C.
move 1 from B to A.
move 2 from B to C.
move 1 from A to C.
move 3 from B to A.
move 1 from C to B.
move 2 from C to A.
move 1 from B to A.
move 4 from B to C.
move 1 from A to C.
move 2 from A to B.
move 1 from C to B.
move 3 from A to C.
move 1 from B to A.
move 2 from B to C.
move 1 from A to C.
A:[]
B:[]
C:[5, 4, 3, 2, 1]
```
