---
title: TSP问题-贪心算法-Python实现-算法分析
date: 2020-06-06 12:45:06
categories: algorithm
tags: [TSP, algorithm, python, 贪心]
---

根据哈工大战德臣老师的计算机专业导论，用Python和贪心算法，解TSP问题，并且分析。

# 概念
## TSP 问题
摘自维基百科：
- 行商问题（最短路径问题）（英語：travelling salesman problem, TSP）
- 给定一系列城市和每对城市之间的距离，求解访问每一座城市一次并回到起始城市的最短回路。
- 它是组合优化中的一个NP困难问题

## 贪心算法
今朝有救今朝醉 - 每次面临选择的时候，选择眼前最优，而不考虑整体是否最优。
使用贪心算法，使其转化为P问题。

<!--more-->

# 数据结构设计
## 给城市编号
- AA -> 0
- BB -> 1
- CC -> 2
- DD -> 3

## 城市间的距离
用二维数组 distance 表示：

|   | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| 0 | - | x | x | x |
| 1 | x | - | x | x |
| 2 | x | x | - | x |
| 3 | x | x | x | - |

那么城市 AA 和 BB 的距离就是：distance[0][1] 或者 distance[1][0]

## 解（访问路径）
用一个一维数组 result 来表示结果：
[0,3,1,2,0,125]: AA -> DD -> BB -> CC -> AA 最后一位表示总路程


# 控制结构（流程）设计
![tsp_flow.jpeg](/images/tsp_flow.jpeg)

# 代码实现
## tsp_greedy.py
```Python
# n: number of city
# result: []
# distance: [][]
def next_city(n, result, distance):
    current_city = result[-1]
    min_distance = None
    next_city = None
    for x in range(n):
        if x in result: continue
        if min_distance is None or distance[current_city][x] < min_distance:
            next_city = x
            min_distance = distance[current_city][x]
    return next_city

# 1. 从0出发
# 2. 在未访问过的城市中选出距离最短的城市
# 3. 将这个城市加到路径里，将这段距离加在总路程里
# 4. 如果路径包含了所有城市，前往5，否则，前往2
# 5. 最后回到0，并加入这段距离
def tsp(n, distance):
    result = [0]
    total = 0
    while(len(result) < n):
        target = next_city(n, result, distance)
        total += distance[result[-1]][target]
        result.append(target)

    total += distance[result[-1]][0]
    result.append(0)
    result.append(total)
    return result
```

## tsp_test.py
```Python
import tsp_greedy
import random

# 根据n，生成用于测试的距离矩阵
def get_distance_matrix(n):
    distance_matrix = [[0 for x in range(n)] for y in range(n)]
    for i in range(n):
        distance_matrix[i][i] = 0
        for j in range(i+1,n):
            distance = random.randint(10,99)
            distance_matrix[i][j] = distance
            distance_matrix[j][i] = distance
        print('from {0} to other cities: {1}'.format(i,distance_matrix[i]))
    return distance_matrix

# Test
n = int(input('input number of cities: '))
distance_matrix = get_distance_matrix(n)

greedy_result = tsp_greedy.tsp(n,distance_matrix)
print('greedy_result:', greedy_result)
```


# 算法分析

## 正确性
满足以下几点：
- 结果包括了所有城市
- 0开头，0结尾
- 除0外，其他城市只出现一次
- 总路程正确

## 与最优解的偏差
最优解的解法为穷举法，代码如下：

```Python
# 生成全排列列表的函数
# [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
def permutation(arr):
    if len(arr) == 0 or len(arr) == 1:
        return [arr]
    result = []
    for i in arr:
        temp_list = arr[:]
        temp_list.remove(i)
        temp = permutation(temp_list)
        for j in temp:
            j[0:0] = [i]    # 将i插入到数组第1位
            result.append(j)
    return result

# 1. 获取n个城市的全排列
# 2. 遍历全排列的每条路径
# 3. 开头不是0的跳过，否则前往4
# 4. 按照路径顺序，累加路程，得到总路程
# 5. 如果此路径的总路程 < 最短总路程，替换最短总路程，并且更新最短路径
# 6. 加入0到路径中
def tsp(n, dist_matrix):

    cities = [x for x in range(n)]
    perm_arr = permutation(cities)

    result = None
    min_distance = None
    for arr in perm_arr:
        if arr[0] != 0: continue    #skip non 0 start arr
        current_distance = 0
        for i in range(len(arr)-1):
            current_distance += dist_matrix[arr[i]][arr[i+1]]
        current_distance += dist_matrix[arr[-1]][0]
        print('for path {0}, distance = {1}'.format(arr, current_distance))
        if min_distance is None or current_distance < min_distance:
            min_distance = current_distance
            result = arr[:]
    result.append(0)
    result.append(min_distance)
    return result
```

通过对比两组的计算结果，可以粗糙的得出一个偏差百分比。
n=5，取对比100次的平均值。跑了大概3分钟，得到的结果是：5.1%

测试代码：

```Python
import tsp_force
import tsp_greedy
import random

# 根据n，生成用于测试的距离矩阵
def get_distance_matrix(n):
    distance_matrix = [[0 for x in range(n)] for y in range(n)]
    for i in range(n):
        distance_matrix[i][i] = 0
        for j in range(i+1,n):
            distance = random.randint(10,99)
            distance_matrix[i][j] = distance
            distance_matrix[j][i] = distance
        print('from {0} to other cities: {1}'.format(i,distance_matrix[i]))
    return distance_matrix

# Test
# n = int(input('input number of cities: '))
n = 5
m = 101

force_total = 0
greedy_total = 0

for i in range(m):
    print('---> test #', i)
    distance_matrix = get_distance_matrix(n)

    force_result = tsp_force.tsp(n,distance_matrix)
    print('force_result:', force_result)
    greedy_result = tsp_greedy.tsp(n,distance_matrix)
    print('greedy_result:', greedy_result)

    force_total += force_result[-1]
    greedy_total += greedy_result[-1]

print('force_total:',force_total)
print('greedy_total:',greedy_total)
print('difference:',(greedy_total - force_total)/force_total)

```


## 复杂度

把注释拿掉，在有涉及到循环到地方标注出来。
其中 next_city 到复杂度为 O(n^2)。
tsp 方法应该为 O(n^3)。

```Python
def next_city(n, result, distance):   
    current_city = result[-1]
    min_distance = None
    next_city = None
    for x in range(n):      ### O(n)
        if x in result: continue    ### O(n^2)
        if min_distance is None or distance[current_city][x] < min_distance:
            next_city = x
            min_distance = distance[current_city][x]
    return next_city

def tsp(n, distance):
    result = [0]
    total = 0
    while(len(result) < n):                     ### O(n)
        target = next_city(n, result, distance)
        total += distance[result[-1]][target]
        result.append(target)

    total += distance[result[-1]][0]
    result.append(0)
    result.append(total)
    return result
```
