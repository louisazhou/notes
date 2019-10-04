# Feature Engineering with Pyspark

{% embed url="https://datacamp-community-prod.s3.amazonaws.com/65076e3c-9df1-40d5-a0c2-36294d9a3ca9" %}

Pyspark 更新很快，所以每次都要记得查版本，不然都不知道documentation不对死在哪里了！

```python
# return spark version
spark.version

# return python version
import sys sys.version_info

# JSON
spark.read.json('example.json') 
# CSV or delimited files
spark.read.csv('example.csv') 
# Parquet
spark.read.parquet('example.parq')

# print column names
print(df.columns)
```



