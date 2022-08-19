# Analyze Cleaned Data

Import the pandas module.
```
import pandas as pd
```

Load the dataset into a dataframe.
```
df = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/LargeData/m2_survey_data.csv")
```

The column ConvertedComp contains Salary converted to annual USD salaries using the exchange rate on 2019-02-01.

This assumes 12 working months and 50 working weeks.

Plot the distribution curve for the column ConvertedComp.
```
import seaborn as sns
sns.distplot(df['ConvertedComp'], hist = False)
```

Plot the histogram for the column ConvertedComp.

```
import numpy as np
count, bin_edges = np.histogram(df['ConvertedComp'].dropna())

df['ConvertedComp'].plot.hist(xticks = bin_edges, figsize = (10,5))
```

Find out if outliers exist in the column ConvertedComp using a box plot
```
df['ConvertedComp'].plot.box(figsize = (10,8))
plt.show
```

Find out the Inter Quartile Range for the column ConvertedComp.
```
IQR = -df['ConvertedComp'].quantile(0.25)+df['ConvertedComp'].quantile(0.75)
IQR
```

Find out the upper and lower bounds.
```
print('lower bounds: ', df['ConvertedComp'].min())
print('uper bounds:', df['ConvertedComp'].max())
```

Identify how many outliers are there in the ConvertedComp column.
```
((df['ConvertedComp']>df['ConvertedComp'].quantile(0.75)+(1.5*(df['ConvertedComp'].quantile(0.75)-df['ConvertedComp'].quantile(0.25)))) |
 (df['ConvertedComp']<df['ConvertedComp'].quantile(0.25)-(1.5*(df['ConvertedComp'].quantile(0.75)-df['ConvertedComp'].quantile(0.25))))).sum()
```

Create a new dataframe by removing the outliers from the ConvertedComp column.
```
outliers = ((df['ConvertedComp']>df['ConvertedComp'].quantile(0.75)+(1.5*(df['ConvertedComp'].quantile(0.75)-df['ConvertedComp'].quantile(0.25)))) |
 (df['ConvertedComp']<df['ConvertedComp'].quantile(0.25)-(1.5*(df['ConvertedComp'].quantile(0.75)-df['ConvertedComp'].quantile(0.25)))))

df[outliers] = np.nan

df.mean()
```

find correlation
```
df.corr()
```
