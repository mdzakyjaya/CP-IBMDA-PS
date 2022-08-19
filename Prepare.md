# Getting Annual Salary Data using Web Scrapping Techniques

define the data source
```
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/labs/datasets/Programming_Languages.html"
```

Import the required libraries
```
from bs4 import BeautifulSoup
import requests
```

Download the webpage at the url
```
data = requests.get(url).text
```

Create a soup object
```
soup = BeautifulSoup(data,'html5lib')
```

Scrape the Language name and annual average salary.
```
table = soup.find('table')
calondf = []

for row in table.find_all('tr'):
    cols = row.find_all('td')
    language = cols[1].getText()
    salary = cols[3].getText()
    calondf.append([language, salary])

import pandas as pd
gajidf = pd.DataFrame(calondf, columns = calondf[0])

gajidf.to_csv('popular-languages.csv') #save the data to csv

pd.read_csv('popular-languages.csv', header = 1) #read the data in csv format
```

output:

| |	Language	| Average Annual Salary |
| --- | --- | --- |
| 1 |	Python |	$114,383 |
| 2 |	Java |	$101,013 |
| 3 |	R |	$92,037 |
| 4 |	Javascript	| $110,981 |
|	5	| Swift	| $130,801 |
| 6 |	C++ |	$113,865 |
| 7	| C# |	$88,726 |
| 8	| PHP |	$84,727 |
| 9	| SQL |	$84,793 |
| 10 |	Go |	$94,082 |
