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

用dic就是这样的效果 {key:value} key必须unique，但是value

```python
score_dict = {'ML': 90, 'Stat': 80, 'Coding': 1, 'Business': 59} # create a dictionary
print(score_dict['ML']) # get ML score
print('Stat' in score_dict) # check if 'Stat' in the dictionary

#如果想要loop over dictionary
for skill in score_dict.values():
  print(skill)
```

