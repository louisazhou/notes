---
description: (unfinished) 循环的iterator，结构化输出
---

# Section1.2 Looping and output format

{% tabs %}
{% tab title="enumerate for idx" %}
```python
skills = ['ML', 'Stat', 'Coding', 'Business']
for idx, skill in enumerate(skills):
  print('{idx_} : {skill_}'.format(skill_ = skill, idx_ = idx)) 


  ···  
output：
0 : ML
1 : Stat
2 : Coding
3 : Business
···
```
{% endtab %}

{% tab title="占位符输出" %}
```python
s1 = 'single quote' # use single quotes

s2= "double quote" # use double quotes 

s4 = '{0} huozhe {1} {2}'.format(s1, s2, 100)

//output：single quote huozhe double quote 100
```
{% endtab %}

{% tab title="enumerate in Set" %}
```python
skills = {'ML', 'Stat', 'Coding', 'Business'}
for idx, skill in enumerate(skills):
  print(f"{skill} : {idx}")
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
1. print中使用{}, 使用.format\(\)格式化输出 括号内可以不包含前面的字段（此时需要一一对应）
2. index可以直接用enumerate，靠idx来call
{% endhint %}

也可以用%d的占位符，就有点C的写法... 

```python
skills = ['ML', 'Stat', 'Coding', 'Business']
scores = [90, 80, 1, 59]

print(scores[skills.index('Coding')])

score_dict = {'ML': 90, 'Stat': 80, 'Coding': 1, 'Business': 59} # create a dictionary

print("current score: %d" % score_dict['Coding'])


#current score: 1
```

```python
skills = {'ML', 'Stat', 'Coding', 'Business'}
skills_list = ['ML', 'Stat', 'Coding', 'Business']

# loop over a set
for idx, skill in enumerate(skills):
  print(f"{skill} : {idx}")

···
Stat : 0
Coding : 1
ML : 2
Business : 3
···
```

