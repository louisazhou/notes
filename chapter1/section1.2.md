---
description: 循环的iterator，结构化输出
---

# Iterators

## Iterators vs. iterables

#### Iterable

* Examples: lists, strings, dictionaries,  file connections 
* An object with an associated `iter()` method 
* Applying `iter()` to an iterable creates an iterator

#### Iterator

* Produces next value with `next()`

An _iterable_ is an object that can return an _iterator_, while an _iterator_ is an object that keeps state and produces the next value when you call `next()` on it. 

### Iterating over iterables \(list\)

```python
# Create a list of strings: flash
flash = ['jay garrick', 'barry allen', 'wally west', 'bart allen']

# Print each list item in flash using a for loop
for person in flash:
    print (person)


# Create an iterator for flash: superhero
superhero=iter(flash)

# Print each item from the iterator
print(next(superhero))
print(next(superhero))
print(next(superhero))
print(next(superhero))
```

### Iterating over dictionaries

```python
pythonistas = {'hugo': 'bowne-anderson', 'francis': 'castro'} 
for key, value in pythonistas.items():
    print(key, value)

# francis castro
# hugo bowne-anderson
```

### Iterating over file connections

```python
file = open('file.txt') 
it = iter(file)
print(next(it))
#This is the first line.
print(next(it))
#This is the second line.
```

### Iterating over iterables \(range\)

Not all iterables are _actual_ lists. You can use `range()` in a `for` loop _as if_ it's a list to be iterated over:

```text
for i in range(5):
    print(i)
```

Recall that `range()` doesn't actually create the list; instead, it creates a range object with an iterator that produces the values until it reaches the limit \(in the example, until the value 4\). 

```python
# Create an iterator for range(10 ** 100): googol
googol = iter(range(10**100))

# Print the first 5 values from googol
print(next(googol))
print(next(googol))
```

### Iterators as function arguments

There are also functions that take iterators and iterables as arguments. For example, the `list()` and `sum()` functions return a list and the sum of elements, respectively.

## enumerate\(\)

`enumerate()` returns an `enumerate` object that produces a sequence of tuples, and each of the tuples is an _index-value_ pair.

```python
avengers = ['hawkeye', 'iron man', 'thor', 'quicksilver'] 
e = enumerate(avengers)
print(type(e))
#<class 'enumerate'>
e_list = list(e) 
print(e_list)
#[(0, 'hawkeye'), (1, 'iron man'), (2, 'thor'), (3, 'quicksilver')]
```

Then, we can use list to turn this enumerate object into a list of tuples and print to see what it contains 

### enumerate\(\) and unpack

```python
avengers = ['hawkeye', 'iron man', 'thor', 'quicksilver'] 
for index, value in enumerate(avengers): #enumerate(avengers, start=10)
print(index, value)
#0 hawkeye
#1 iron man
#2 thor
#3 quicksilver
```

## zip\(\)

`zip()` takes any number of iterables and returns a `zip` object that is an iterator of tuples.

```python
avengers = ['hawkeye', 'iron man', 'thor', 'quicksilver'] 
names = ['barton', 'stark', 'odinson', 'maximoff']
z = zip(avengers, names) 
print(type(z))
# <class 'zip'>
z_list = list(z) 
print(z_list)
#[('hawkeye', 'barton'), ('iron man', 'stark'), ('thor', 'odinson'), ('quicksilver', 'maximoff')]
```

### zip\(\) and unpack

```python
avengers = ['hawkeye', 'iron man', 'thor', 'quicksilver'] 
names = ['barton', 'stark', 'odinson', 'maximoff']
for z1, z2 in zip(avengers, names): 
    print(z1, z2)

# hawkeye barton
# iron man stark
# thor odinson quicksilver maximoff
```

### zip\(\*object\)

use `*` in a call to `zip()` to unpack the tuples produced by `zip()`.

```python
z1 = zip(mutants, powers)

# 'Unzip' the tuples in z1 by unpacking with * and zip(): result1, result2
result1, result2 = zip(*z1)
```

### Print zip with \*

```python
avengers = ['hawkeye', 'iron man', 'thor', 'quicksilver'] 
names = ['barton', 'stark', 'odinson', 'maximoff']
z = zip(avengers, names)
print(*z)

#('hawkeye', 'barton') ('iron man', 'stark') ('thor', 'odinson') ('quicksilver', 'maximoff')
```

## \* operator \(splat operator\)

```python
word = 'Data' 
it = iter(word)
print(*it)
#  D  a t a 
```

## Use iterator to load data in chunks









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

