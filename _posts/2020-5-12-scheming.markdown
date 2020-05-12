---
layout: post
title:  "scheming: a pyspark schema helper"
date:   2020-5-12
description: "motivation for scheming from github readme.md"
---
## Motivation

Nearly every pyspark example I've ever come across has used inferSchema=True:
```python
df = spark.read.csv("./example.csv", header=True, inferSchema=True)
```
but for Production workloads the best practice is to define the schema explicitly:
```python
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

schema = StructType([StructField("fname",StringType(),True),StructField("lname",StringType(),True),StructField("role",StringType(),True),StructField("year",IntegerType(),True),StructField("comment",StringType(),True)])

df = spark.read.csv("./example.csv", header=True, schema=schema)
```
even this isn't too bad, except when you get into hundreds of CSV files and your Python becomes page after page of explicit schema definitions.

scheming handles the persistence and loading of pyspark schemas:
```python
# try and load the schema from the file and indicate success
schema, inferSchema = scheming.load(schema_filename)

# use both results
df = spark.read.csv(csv_filename, header=True, schema=schema, inferSchema=inferSchema)
# and if schema was inferred then persist the schema to a JSON file
# so it can be reviewed, tweaked, and used next time
if inferSchema:
    scheming.save(schema_filename, df.schema)
    print(f"WARN: inferSchema=True on {csv_filename}")
```
and this allows the metadata attribute of each field to be populated with a dictionary as well as data types to be adjusted if infer schema got it wrong.

scheming is on github [here](https://github.com/InterruptSpeed/scheming).