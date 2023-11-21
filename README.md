# Eat Safe, Love Data Analysis

## Overview

This repository contains code and instructions for the "Eat Safe, Love" data analysis challenge. The challenge involves setting up a MongoDB database, performing updates, and conducting exploratory analysis on food establishment data.

## Table of Contents

- [Setup](#setup)
  - [Database and Jupyter Notebook Setup](#database-and-jupyter-notebook-setup)
  - [Updating the Database](#updating-the-database)
  - [Exploratory Analysis](#exploratory-analysis)
  - [Question 1](#question-1)
  - [Question 2](#question-2)
  - [Question 3](#question-3)
  - [Question 4](#question-4)

## Setup

### Database and Jupyter Notebook Setup

#### Importing Data

To import the dataset, use the following command in your terminal:

```bash
mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json
```

## Jupyter Notebook Setup

# Import dependencies
from pymongo import MongoClient
from pprint import pprint
import pandas as pd

# Create an instance of MongoClient
mongo = MongoClient(port=27017)

# Assign the uk_food database to a variable name
db = mongo['uk_food']

# Review the collections in our database
print(db.list_collection_names())

# Assign the collection to a variable
establishments = db['establishments']

# Updating the Database
## Adding a New Restaurant

# Create a dictionary for the new restaurant data
new_restaurant_data = {
    # ... (restaurant details)
}

# Insert the new restaurant into the collection
establishments.insert_one(new_restaurant_data)

- Find the BusinessTypeID for "Restaurant/Cafe/Canteen"
- Update the new restaurant with the correct BusinessTypeID
- Remove establishments in Dover Local Authority
- Convert latitude, longitude, and RatingValue to numbers
- (Refer to the provided code for detailed implementation)

## Exploratory Analysis
### Question 1

# Find establishments with a hygiene score of 20
# Count documents, display the first document, and convert the result to a Pandas DataFrame

```python
# Execute the query
cursor = establishments.find({"scores.Hygiene": 20})

# Use count_documents to display the number of documents in the result
result_count = establishments.count_documents({"scores.Hygiene": 20})
print("Number of establishments with a hygiene score of 20:", result_count)

# Display the first document in the results using pprint
first_document = establishments.find_one({"scores.Hygiene": 20})
pprint(first_document)

# Convert the result to a Pandas DataFrame
df = pd.DataFrame(list(cursor))

# Display the number of rows in the DataFrame
print("Number of rows in the DataFrame:", len(df))

# Display the first 10 rows of the DataFrame
print(df.head(10))
```

### Question 2

# Find establishments in London with RatingValue >= 4
# Count documents, display the first document, and convert the result to a Pandas DataFrame

```python
# Execute the query
cursor = establishments.find({'LocalAuthorityName': {'$regex' : 'London'} , 'RatingValue': {'$gte' : '4'}})

# Use count_documents to display the number of documents in the result
rating_value = establishments.count_documents({'LocalAuthorityName': {'$regex' : 'London'} , 'RatingValue': {'$gte' : '4'}})
print(f'There are {rating_value} restaurants with a rating value greater than or equal to 4.')

# Display the first document in the results using pprint
pprint(establishments.find_one({'LocalAuthorityName': {'$regex' : 'London'} , 'RatingValue': {'$gte' : '4'}}))

# Convert the result to a Pandas DataFrame
query = {'LocalAuthorityName': {'$regex' : 'London'} , 'RatingValue': {'$gte' : '4'}}
results = establishments.find(query)
df = pd.DataFrame(results)

# Display the number of rows in the DataFrame
num_rows = df['_id'].count()
print(num_rows)

# Display the first 10 rows of the DataFrame
df.head(10)
```

### Question 3

# Find top 5 establishments with RatingValue 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"
# Display results using pprint and convert the result to a Pandas DataFrame

```python
# Execute the query and sort the results
cursor = establishments.find(query).sort(sort).limit(5)

# Iterate over the cursor and print the results
for doc in cursor:
    pprint(doc)

# Convert result to Pandas DataFrame
cursor = establishments.find(query).sort(sort).limit(5)

# Convert the cursor to a list of dictionaries
results = list(cursor)

# Create a Pandas DataFrame from the list of dictionaries
new_df = pd.DataFrame(results)

# Display the Pandas DataFrame
new_df
```

### Question 4

# Find establishments in each Local Authority with hygiene score of 0
# Display results using pprint and convert the result to a Pandas DataFrame

```python
# Create a pipeline that: 
# 1. Matches establishments with a hygiene score of 0
# 2. Groups the matches by Local Authority
# 3. Sorts the matches from highest to lowest

# Print the number of documents in the result
pipeline = [
    {
        "$match": {
            "scores.Hygiene": 0
        }
    },
    {
        "$group": {
            "_id": "$LocalAuthorityName",
            "count": {"$sum": 1}
        }
    },
    {
        "$sort": {"count": -1}
    }
]

results = list(establishments.aggregate(pipeline))

# Print the number of documents in the result
print(f"Total number of establishments with a hygiene score of 0: {len(results)}")

# Print the first 10 results
for result in results[:10]:
    pprint(result)
```

