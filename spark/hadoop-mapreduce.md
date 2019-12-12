# Hadoop MapReduce

以“统计一个无限不循环小数的小数位数字出现频率”为例

{% code title="Mapper function" %}
```python
import sys

linecount=0
# Get input lines from stdin
for line in sys.stdin:
	# Remove spaces from beginning and end of the line
    line = line.strip()
    
    # Remove the 2., and only keep the decimals 
    if linecount == 0:
        line = line[2:]
	
    # Split it into list of numbers
    numbers = list(line)
	# Output tuples on stdout
    for number in numbers:
        print ('%s\t%s' % (number, "1"))
        
    linecount+=1
```
{% endcode %}

mapper得到一大堆key-value pairs, 分别是每一个位置出现的数字以及出现的个数（1次）。

{% code title="Reducer function" %}
```python
import sys
 
# Create a dictionary to map numbers to counts
numbercount = {}
 
# Get input from stdin
for line in sys.stdin:
    #Remove spaces from beginning and end of the line
    line = line.strip()
 
    # parse the input from mapper.py
    number, count = line.split('\t', 1)
    # convert count (currently a string) to int
    try:
        count = int(count)
        number = int(number)
    except ValueError:
        continue
 
    try:
        numbercount[number] = numbercount[number]+count
    except:
        numbercount[number] = count
 

sumnumber, countnumber =  0, 0
for number in numbercount.keys():
    # Write the tuples to stdout
    # Currently tuples are unsorted
    sumnumber += number*numbercount[number]
    countnumber += numbercount[number]
    print ('%s\t%s'% ( number, numbercount[number] ))

# Now they are sorted by the values, in increasing order     
print(sorted(numbercount.items(), key=lambda kv:(kv[1], kv[0])))

# Calculate the average of the digits
print('avg=', sumnumber/countnumber)
```
{% endcode %}

结果是 一堆key value pairs 如果用以上的map reduce来处理sqrt2，那么得到的结果是

```python
7	92
1	89
8	98
2	95
4	96
5	78
9	108
0	95
3	106
6	93
```

