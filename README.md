# Apache_Solr_Basic_Operations_using_Python
## Problem Statement
1.createCollection(p_collection_name)
  Using Any of the programming language implement below functions
2.indexData(p_collection_name, p_exclude_column):
  Index the given employee data into the specified collection, excluding the column provided in     
  p_exclude_column.
3.searchByColumn(p_collection_name, p_column_name, p_column_value):
  Search within the specified collection for records where the column p_column_name matches the value   
  p_column_value.
4.getEmpCount(p_collection_name)
5.delEmpById(p_collection_name, p_employee_id)
6.getDepFacet(p_collection_name):
  Retrieve the count of employees grouped by department from the specified collection.

## Program
```python3
import json
import requests
import pandas as pd

#Collection Creation
def create_collection(p_collection_name):
    url=f"http://localhost:8983/solr/admin/collections?action=CREATE&name={p_collection_name}&numShards=1&replicationFactor=1"
    print(requests.get(url).json())
    url = f"http://localhost:8983/solr/{p_collection_name}/schema"
    headers = {"Content-Type": "application/json"}
    data = {
    "add-field": {
      "name": "Department",
      "type": "string",
      "multiValued": False
    }
  }
    response = requests.post(url, headers=headers, data=json.dumps(data))
    print(f"Create Collection Response: {response.status_code}")

#Indexing Data
def indexData(p_collection_name,p_exclude_column):
    data=pd.read_csv("Employee.csv",encoding='latin-1')
    data = data.drop(columns=[p_exclude_column])
    data.columns = data.columns.str.replace(' ', '_')
    data=data.to_csv(index=False,encoding='utf-8')
    url=f"http://localhost:8983/solr/{p_collection_name}/update?commit=true"
    headers={"Content-Type":"application/csv"}
    response=requests.post(url,headers=headers,data=data)
    print(data)
    print(response)
    print(f"Response Status Code: {response.status_code}")
    print(f"Response Content: {response.text}")
    return response.json()


#Search By Column
def searchByColumn(p_collection_name,p_column_name,p_column_value):
    url=f"http://localhost:8983/solr/{p_collection_name}/select?indent=true&q={p_column_name}:{p_column_value}&wt=json"
    response = requests.get(url)
    results = response.json()['response']['docs']
    formatted_json = json.dumps(results, indent=4)
    print(formatted_json)

#Getting Employee Count
def getEmpCount(p_collection_name):
    url = f"http://localhost:8983/solr/{p_collection_name}/select?q=*:*&rows=0"
    response = requests.get(url)
    return response.json()['response']['numFound']

#Deleting Employee by ID
def delEmpById(p_collection_name, p_employee_id):
    url = f"http://localhost:8983/solr/{p_collection_name}/update?commit=true"
    data = json.dumps({'delete':{'query':f"Employee_ID:{p_employee_id}"}})
    headers = {"Content-Type": "application/json"}
    response = requests.post(url, headers=headers, data=data)
    print("Response Status Code:", response.status_code)
    print("Response Content:", response.text) 
    return response.json()

#Getting Count for the Groups under a Department
def getDepFacet(p_collection_name):
    url = f"http://localhost:8983/solr/{p_collection_name}/select?q=*:*&facet=true&facet.field=Department"
    response = requests.get(url)
    facets = response.json()['facet_counts']['facet_fields']['Department']
    print(f"Department Facets: {facets}")

```
