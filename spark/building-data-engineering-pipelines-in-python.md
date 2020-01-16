# Building Data Engineering Pipelines in Python

{% embed url="https://www.datacamp.com/courses/building-data-engineering-pipelines-in-python" %}

The landing zone contains raw data, the clean zone contains clean data, and the business zone contains domain-specific data, usually related to solve business problems.

![](../.gitbook/assets/image%20%2822%29.png)

parquet文件也可以是csv，用file filename可以看到file的类型。

type\(catalog\["diaper\_reviews"\].read\(\)\)可以看到file的内部结构。

### Singer’s core concepts

Aim: “The open-source standard for writing scripts that move data”

Singer is a specication 

* data exchange format: JSON
* extract and load with taps and targets =&gt; language independent
* communicate over streams:  schema \(metadata\) state \(process metadata\)  record \(data\)

![](../.gitbook/assets/image%20%2869%29.png)

```python
columns = ("id", "name", "age", "has_children") 

users = {(1, "Adrian", 32, False),
        (2, "Ruanne", 28, False), 
        (3, "Hillary", 29, True)}

json_schema = {
"properties": {"age": {"maximum": 130, "minimum": 1,
"type": "integer"},
        "has_children": {"type": "boolean"}, 
        "id": {"type": "integer"},
        "name": {"type": "string"}},
"$id": "http://yourdomain.com/schemas/my_user_schema.json", 
"$schema": "http://json-schema.org/draft-07/schema#"}

schema = {'properties': {
    'brand': {'type': 'string'},
    'model': {'type': 'string'},
    'price': {'type': 'number'},
    'currency': {'type': 'string'},
    'quantity': {'type': 'integer', 'minimum': 1},  
    'date': {'type': 'string', 'format': 'date'},
    'countrycode': {'type': 'string', 'pattern': "^[A-Z]{2}$"}, 
    'store_name': {'type': 'string'}}}

# Write the schema to stdout
singer.write_schema(stream_name='products', schema=schema, key_properties=[])
```

```python
# Describing the data through its schema
import singer 

singer.write_schema(schema=json_schema,
    stream_name='DC_employees', 
    key_properties=["id"])
    
#Serializing JSON
import json 
json.dumps(json_schema["properties"]["age"])

with open("foo.json", mode="w") as fh:
json.dump(obj=json_schema, fp=fh) # writes the json-serialized object
# to the open file handle  
```

{% embed url="https://www.datacamp.com/courses/creating-robust-python-workflows" %}



