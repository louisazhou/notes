# List Comprehensions

Say, there is a list, doctor = \['house', 'cuddy', 'chase', 'thirteen', wilson\], the list comprehension `[doc[0] for doc in doctor]`  produces the list `['h', 'c', 'c', 't', 'w']`.

### Nested List Comprehension 

```python
matrix = [[col for col in range(5)] for row in range(5)]

#[[0, 1, 2, 3, 4],[0, 1, 2, 3, 4],[0, 1, 2, 3, 4],[0, 1, 2, 3, 4],[0, 1, 2, 3, 4]]

#也可以只打印单独每一行
for row in matrix:
    print(row)          
```

To create the list of lists, you simply have to supply the list comprehension as the **output expression** of the overall list comprehension:

`[`\[_output expression_\] `for` _iterator variable_ `in` _iterable_`]`

### Conditionals in comprehensions 

new\_nums = \[num + 1 for num in nums\]

