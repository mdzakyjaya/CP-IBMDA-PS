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

# Retrieve Jobs Posting using Web API Technique
note: this is just demo of getting data using web API, the server used in this session is a development server

## API implementation using FLASK
```
!pip install flask
```

get the data source
```
!wget  https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/labs/module%201/Accessing%20Data%20Using%20APIs/jobs.json
```

```
import flask
from flask import request, jsonify
import requests
import re

def get_data(key,value,current):
    results = list()
    pattern_dict = {
        'C'      : '(C)',
        'C++'    : '(C\+\+)',
        'Java'   :'(Java)',
        'C#'     : '(C\#)',
        'Python' :'(Python)',
        'Scala' : '(Scala)',
        'Oracle' : '(Oracle)',
        'SQL Server': '(SQL Server)',
        'MySQL Server' :'(MySQL Server)',
        'PostgreSQL':'(PostgreSQL)',
        'MongoDB'    : '(MongoDB)',
        'JavaScript'    : '(JavaScript)',
        'Los Angeles' :'(Los Angeles)',
        'New York':'(New York)',
        'San Francisco':'(San Francisco)',
        'Washington DC':'(Washington DC)',
        'Seattle':'(Seattle)',
        'Austin':'(Austin)',
        'Detroit':'(Detroit)',
        }
    for rec in current:
        print(rec[key])
        print(type(rec[key]))
        print(rec[key].find(value))
        #if rec[key].find(value) != -1:
        import re
        #reex_str = """(C)|(C\+\+)|(JavaScript)|(Java)|(C\#)|(Python)|(Scala)|(Oracle)|(SQL Server)|(MySQL Server)|(PostgreSQL)|(MongoDB)"""
        if re.search(pattern_dict[value],rec[key]) != None:
            results.append(rec)
    return results

app = flask.Flask(__name__)

import json
data = None
with open('jobs.json',encoding='utf-8') as f:
    # returns JSON object as
    # a dictionary
    data = json.load(f)
    
@app.route('/', methods=['GET'])
def home():
    
    return '''<h1>Welcome to flask JOB search API</p>'''

@app.route('/data/all', methods=['GET'])
def api_all():
    return jsonify(data)


@app.route('/data', methods=['GET'])
def api_id():
    # Check if keys such as Job Title,KeySkills, Role Category and others  are provided as part of the URL.
    #  Assign the keys to the corresponding variables..
    # If no key is provided, display an error in the browser.
    res = None
    for req in request.args:
        
        if req == 'Job Title':
            key = 'Job Title'
        elif req == 'Job Experience Required' :
            key='Job Experience Required'
        elif req == 'Key Skills' :
            key='Key Skills'
            
        elif req == 'Role Category' :
            key='Role Category'
        elif req == 'Location' :
            key='Location'
        
        elif req == 'Functional Area' :
            key='Functional Area'
        
        elif req == 'Industry' :
            key='Industry'
        elif req == 'Role' :
            key='Role'
        elif req=="id":
             key="id"
        else:
            pass
    
        value = request.args[key]
        if (res==None):
            res = get_data(key,value,data)
        else:
            res = get_data(key,value,res)

    # Use the jsonify function from Flask to convert our list of
    # Python dictionaries to the JSON format.
    return jsonify(res)

app.run()
```

## Collecting Jobs Data using API

import required libraries
```
import pandas as pd
import json
```

define the function
```
api_url="http://127.0.0.1:5000/data"
def get_number_of_jobs_T(technology):
    number_of_jobs = 0
    payload = {'key skills':technology,'page':1}           #parameters to search the api
    while True:                                             #Loop to get the data of multiples pages
        r = requests.get(api_url, params=payload)           #Response to be saved
        if r.ok:                                            #to discard errors
            data = r.json()                                 #retrieved data to be counted
            number_of_jobs = number_of_jobs + len(data)     #count of data
            payload["page"] = payload["page"] + 1           #go to the next page
        else:
            break                                           #exit to the loop in case the request is not "ok"
        if len(data) != 50:
            break                                           #exit the loop if the data is less than 50 elements, meaning that is the last page.
    return technology, number_of_jobs
```

```
api_url="http://127.0.0.1:5000/data"
def get_number_of_jobs_T(technology):
    payload={"Key Skills": technology}
    response=requests.get(api_url, params=payload)
    if response.ok:
        data=response.json()
#         print(data)
        number_of_jobs = len(data)
    
    return technology,number_of_jobs
```

Calling the function for Python and checking if it works.
```
get_number_of_jobs_T("Python")
```

create a function to find number of jobs in US for a specified location
```
def get_number_of_jobs_L(location):
    payload = {'Location':location}
    response = requests.get(api_url, params = payload)
    if response.ok:
        data = response.json()
        number_of_jobs = len(data)
    return location,number_of_jobs
```

check if it is working.
```
get_number_of_jobs_L('Los Angeles')
```

prepare to extract data to excel workbook

Import libraries required to create excel spreadsheet
```
from openpyxl import Workbook
```

Create a workbook and select the active worksheet
```
wb=Workbook()
ws=wb.active
ws.append(location)
```

create funtion to get the number of jobs in specified location and programming language
```
def get_number_of_jobs_TL(technology, location):
    number_of_jobs_list = []
    for location in location:
        payload={"Key Skills": technology, "Location": location}
        response=requests.get(api_url, params=payload)
        if response.ok:
            data=response.json()
            number_of_jobs = len(data)
            number_of_jobs_list.append(number_of_jobs)
    return number_of_jobs_list
    return ws.append(number_of_jobs_list)
```

define the locations
```
location = ['Los Angeles', 'New York', 'San Francisco', 'Washington DC', 'Seattle', 'Austin', 'Detroit']
```

find the number of job postings for python in predifined location
```
get_number_of_jobs_TL("Python", location)
```

Save into an excel spreadsheet
```
wb.save('job-postings.xlsx')
```
