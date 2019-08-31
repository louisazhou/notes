# Python Good to knows

1. 在Google Colaboratory 可以使用一些Linux命令，比如

```bash
!pip install pandasql 
```

安装一些Colab上没有的包

2. 在Colab上可以用\#@title +Title做标题

3. 在Python里可以 print\(1 or False\)这样的语句，它返回的是 第一个不是false的值

4. 对于string的一些简便操作

```python
.capitalize()     #大写首字母
.upper()          #所有字母大写
.count('o')       #数o有几个
.index('te')      #给出te的index
```

5. Python List的elements可以不相同，但是np array就不行；比如可以有一个

```python
lst3 = ['one', 2, 'three', 4]
```

6. Python List是mutable的

7. +号可以用来连接字符串，也可以用来连接两个list

```python
lst2 = [1, 2, 3, 4] 

print(lst2 + lst3)

s1 = 'single quote' # use single quotes
s2= "double quote" # use double quotes 
x = 100
s3 = s1 + ' huozhe ' + s2 + str(x) #如果是str的加，要把数值型转成字符型才能+
print(s3)
```

8. list2=list1\[:\]是shallow copy，lst4 = lst2.copy\(\)是hard copy，lst5 = lst2是copy reference

9. Python list或string的切片（slicing）的规矩是\[start\(inclusive\) : end\(exclusive\) : step\] 

10. lst\[::-1\]可以reverse a list

11. slicing的过程也可以改变数值，比如`lst[2:4] = [3, 2]`

12. Python的Dictionary

如果我们想要索引一个成绩，用两个list的话效果是这样的 时间复杂度是O\(n\)

```python
skills = ['ML', 'Stat', 'Coding', 'Business']
scores = [90, 80, 1, 59]

print(scores[skills.index('Coding')])
```

用dic就是这样的效果 {key:value} key必须unique，但是value可以是任意一个东西

```python
score_dict = {'ML': 90, 'Stat': 80, 'Coding': 1, 'Business': 59} # create a dictionary
print(score_dict['ML']) # get ML score
print('Stat' in score_dict) # check if 'Stat' in the dictionary

#如果想要loop over dictionary
for skill in score_dict.values():
  print(skill)
```

13. 不想看那些warnings

```python
import warnings
warnings.filterwarnings('ignore')
```

14. numpy的random seed和python的random seed不是一个东西，所以 random.seed = xxx 和np.random.seed=xxx 也是两个东西

15. \t \n \r 这些C语言的东西在Python里照样能用，\t可以自动对齐

16. 在Python中使用非ASICC字符，比如 中文

```python
#!usr/bin/python
# -*- coding: UTF-8 -*-
print "现在可以中文了"
```

17. Python的list支持各种类型，也支持混着，所以使用的时候要留意判断

18. remove list by index 用的是del; O\(n\)的操作，因为后面的要往左移，但是假如本来要删除的就是最后一个，那O\(1\)

而list.remove\(\) 是remove by value，而且是删除第一个出现的，O\(n\)的操作

```python
del list[1]
list.remove(1)
```

19. 看懂python doc  
- \[, xxx\]代表变量可忽略

\*arg 代编不确定个数的参数，进入函数之后是一个tuple

\*\*args 更灵活，通过var\_name=var\_value调用，进入函数后是dict

[链接](https://stackoverflow.com/questions/400739/what-does-asterisk-mean-in-python)

20. 在函数括号传的值里可以放default value，在没有值传进来的时候就用default，有值传进来的时候就用传入的值

21. 在python2里 range\(\)占O\(n\)的空间，xrange\(\)占O\(1\)的空间 

22. 如果有一些column在打印的时候被...了 想看到全部，用

```python
np.set_printoptions(threshold = np.nan) 
```

或者用

```python
pd.set_option('display.max_columns', 500)
```

23. 针对整个Jupiter notebook的打印格式，可以用

```python
pd.set_option('display.float_format', lambda x: '%.3f' % x)
```

24. Python有个内置函数，enumerate\(\), 可以帮助遍历，它可以获取index和value

```python
list1 = ["1", "2", "3", "4"]
for index, item in enumerate(list1):
    print index, item
>>>
0 1
1 2
2 3
3 4
```

enumerate还可以接收第二个参数，用于指定索引起始值

```python
list1 = ["1", "2", "3", "4"]
for index, item in enumerate(list1, 1):
    print index, item
>>>
1 1
2 2
3 3
4 4
```

还可以用enumerate统计文件的行数

```python
ount = 0
for index, line in enumerate(open(filepath,'r'))： 
    count += 1
```

