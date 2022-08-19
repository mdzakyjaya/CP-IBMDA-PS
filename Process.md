  # Data Wrangling (Data Cleaning or Data Preprocessing)
	
Import pandas module.
```
import pandas as pd
```

Load the dataset into a dataframe.
```
df = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/LargeData/m1_survey_data.csv")
```

Find how many duplicate values
```
df.duplicated(keep = 'first').sum()
```

Remove the duplicate rows from the dataframe.
```
df.drop_duplicates(ignore_index = True, inplace = True)
```

Verify if duplicates were actually dropped.
```
df.duplicated().sum()
```

check how many records are remain after removing duplicate values
```
df['Respondent'].nunique()
```

Find out how many rows are missing in the column 'WorkLoc'
```
df['WorkLoc'].isnull().sum()
```

dealing with missing values

Find the value counts for the column with missing values.
```
df['WorkLoc'].value_counts()
```

replace all the empty rows in the column WorkLoc with the majority value.
```
df['WorkLoc'].fillna(value = 'Office', inplace = True)
```

Verify if dealing was successful.
```
df['WorkLoc'].isnull().sum()
```

Normalizing data

There are two columns in the dataset that talk about compensation.

One is "CompFreq". This column shows how often a developer is paid (Yearly, Monthly, Weekly).

The other is "CompTotal". This column talks about how much the developer is paid per Year, Month, or Week depending upon his/her "CompFreq".

This makes it difficult to compare the total compensation of the developers.

In this section you will create a new column called 'NormalizedAnnualCompensation' which contains the 'Annual Compensation' irrespective of the 'CompFreq'.

Once this column is ready, it makes comparison of salaries easy.

---------------------------------------------
List out the various categories in the column 'CompFreq'
```
df['CompFreq'].value_counts()
```

Create a new column named 'NormalizedAnnualCompensation'.
```
df['CompFreq'].replace(to_replace = 'Yearly', value = 1, inplace = True)
df['CompFreq'].replace(to_replace = 'Monthly', value = 12, inplace = True)
df['CompFreq'].replace(to_replace = 'Weekly', value = 52, inplace = True)

df['NormalizedAnnualCompen'] = df['CompFreq']*df['CompTotal']
df[['NormalizedAnnualCompen', 'CompFreq', 'CompTotal']]
```
