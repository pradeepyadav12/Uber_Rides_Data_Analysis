# Uber Rides Data Analysis using Python

![Uber Logo](https://github.com/pradeepyadav12/Uber_Rides_Data_Analysis/blob/main/UberImage.png)

## Overview:
The "Uber Rides Data Analysis using Python" project leverages Python libraries like Pandas for data manipulation and Matplotlib or Seaborn for visualization. The dataset is explored through techniques like exploratory data analysis (EDA) to uncover patterns in ride distances and frequencies. The project delivers a comprehensive analysis, supported by visualizations, enabling stakeholders to make data-driven decisions for improved service and operational efficiency.

## Objectives:
- Analyze Uber Ride Data: Examine ride distance and frequency patterns using Python.
- Identify Key Metrics: Determine common ride distances, highlighting that most trips range between 0-20 miles.
- Visualize Data: Create clear and informative visualizations, such as histograms or bar charts, to represent ride distribution.
- Provide Actionable Insights: Help stakeholders understand user behavior to optimize operations and enhance services.


## Dataset
The data for this project is sourced from the kaggle dataset:
- **Dataset Link:** [Uber Rides Dataset](https://www.kaggle.com/datasets/zusmani/uberdrives?resource=download)

## Importing all Libraries
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

## Importing Dataset
```
dataset = pd.read_csv("UberDataset.csv")
dataset.head()
```
## shape of the dataset
```
dataset.shape
```

## Information of dataset
```
dataset.info()
```

## null values to Not
```
dataset['PURPOSE'].fillna("NOT", inplace=True)
```
## date time format
```
dataset['START_DATE'] = pd.to_datetime(dataset['START_DATE'], 
                                       errors='coerce')
dataset['END_DATE'] = pd.to_datetime(dataset['END_DATE'], 
                                     errors='coerce')
```

## Converting the time into categories
```
from datetime import datetime

dataset['date'] = pd.DatetimeIndex(dataset['START_DATE']).date
dataset['time'] = pd.DatetimeIndex(dataset['START_DATE']).hour

#changing into categories of day and night
dataset['day-night'] = pd.cut(x=dataset['time'],
                              bins = [0,10,15,19,24],
                              labels = ['Morning','Afternoon','Evening','Night'])
```

## Drop row of null values
```
dataset.dropna(inplace=True)
```

## drop the duplicates rows
```
dataset.drop_duplicates(inplace=True)
```

## unique values in dataset
```
obj = (dataset.dtypes == 'object')
object_cols = list(obj[obj].index)

unique_values = {}
for col in object_cols:
  unique_values[col] = dataset[col].unique().size
unique_values
```

## we will be using matplotlib and seaborn library for countplot the CATEGORY and PURPOSE columns
```
plt.figure(figsize=(10,5))

plt.subplot(1,2,1)
sns.countplot(dataset['CATEGORY'])
plt.xticks(rotation=90)

plt.subplot(1,2,2)
sns.countplot(dataset['PURPOSE'])
plt.xticks(rotation=90)
```

## time column
```
sns.countplot(dataset['day-night'])
plt.xticks(rotation=90)
```

## we will be comparing the two different categories along with the PURPOSE of the user.
```
plt.figure(figsize=(15, 5))
sns.countplot(data=dataset, x='PURPOSE', hue='CATEGORY')
plt.xticks(rotation=90)
plt.show()
```

## CATEGORY and PURPOSE columns are two very important columns and we will be using OneHotEncoder to categories them.
```
from sklearn.preprocessing import OneHotEncoder
object_cols = ['CATEGORY', 'PURPOSE']
OH_encoder = OneHotEncoder(sparse=False, handle_unknown='ignore')
OH_cols = pd.DataFrame(OH_encoder.fit_transform(dataset[object_cols]))
OH_cols.index = dataset.index
OH_cols.columns = OH_encoder.get_feature_names_out()
df_final = dataset.drop(object_cols, axis=1)
dataset = pd.concat([df_final, OH_cols], axis=1)

# This code is modified by Susobhan Akhuli
```

## correlation between the columns using heatmap.
```
# Select only numerical columns for correlation calculation
numeric_dataset = dataset.select_dtypes(include=['number'])

sns.heatmap(numeric_dataset.corr(), 
            cmap='BrBG', 
            fmt='.2f', 
            linewidths=2, 
            annot=True)

# This code is modified by Susobhan Akhuli
```

## visualize the month data
```
dataset['MONTH'] = pd.DatetimeIndex(dataset['START_DATE']).month
month_label = {1.0: 'Jan', 2.0: 'Feb', 3.0: 'Mar', 4.0: 'April',
               5.0: 'May', 6.0: 'June', 7.0: 'July', 8.0: 'Aug',
               9.0: 'Sep', 10.0: 'Oct', 11.0: 'Nov', 12.0: 'Dec'}
dataset["MONTH"] = dataset.MONTH.map(month_label)

mon = dataset.MONTH.value_counts(sort=False)

# Month total rides count vs Month ride max count
df = pd.DataFrame({"MONTHS": mon.values,
                   "VALUE COUNT": dataset.groupby('MONTH',
                                                  sort=False)['MILES'].max()})

p = sns.lineplot(data=df)
p.set(xlabel="MONTHS", ylabel="VALUE COUNT")
```

## Visualization for days data
```
dataset['DAY'] = dataset.START_DATE.dt.weekday
day_label = {
    0: 'Mon', 1: 'Tues', 2: 'Wed', 3: 'Thus', 4: 'Fri', 5: 'Sat', 6: 'Sun'
}
dataset['DAY'] = dataset['DAY'].map(day_label)
```
```
day_label = dataset.DAY.value_counts()
sns.barplot(x=day_label.index, y=day_label);
plt.xlabel('DAY')
plt.ylabel('COUNT')
```

## let’s explore the MILES Column. We can use boxplot to check the distribution of the column.
```
sns.boxplot(dataset['MILES'])
```

## Let’s zoom in it for values lees than 100.
```
sns.boxplot(dataset[dataset['MILES']<100]['MILES'])
```

## It’s bit visible. But to get more clarity we can use distplot for values less than 40.
```
sns.distplot(dataset[dataset['MILES']<40]['MILES'])
```


