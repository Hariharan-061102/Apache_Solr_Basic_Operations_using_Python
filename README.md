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
    print(response.json()['response']['numFound'])

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

v_nameCollection="Hash_Hariharan"
v_phoneCollection='Hash_4791'
create_collection(v_nameCollection)
create_collection(v_phoneCollection)
getEmpCount(v_nameCollection)
indexData(v_nameCollection,'Department')
indexData(v_ phoneCollection, 'Gender')
delEmpById(v_nameCollection,'E02003')
getEmpCount(v_nameCollection)
searchByColumn(v_nameCollection,'Department','IT')
searchByColumn(v_nameCollection,'Gender' ,'Male')
searchByColumn(v_ phoneCollection,'Department','IT')
getDepFacet(v_nameCollection)
getDepFacet(v_phoneCollection)
```

## OUTPUT:
### After starting the solr
![solr_start](https://github.com/user-attachments/assets/5844c6b8-cce3-40da-8519-6acdababe3ec)
### Creating Collection using the defined Function
![creating_collection_terminal](https://github.com/user-attachments/assets/e56b50d2-aee2-4451-b1c4-7c559cb69d3d)
### Relfection of created Collections on the Site
![collections_on_site](https://github.com/user-attachments/assets/b1469247-c75e-4fcd-8f46-404ce0162747)
### Employee Count before indexing the document
![4 Emp_before_indexing](https://github.com/user-attachments/assets/5eb0a12d-9f66-40c0-b5cf-680d28f84528)
### NameCollection Before indexing 
![before_indexing1](https://github.com/user-attachments/assets/3d3c5e23-e31f-44bd-9cec-076010d790b7)
### PhoneCollection Before indexing
![before_indexing2](https://github.com/user-attachments/assets/0a19724b-6413-4a2e-b649-019c59ede985)
## Indexing the document using the function
![indexing_terminal](https://github.com/user-attachments/assets/61acd35d-22f0-468d-8e63-7f8d81209bfa)
### NameCollection After indexing 
![af_index_department](https://github.com/user-attachments/assets/e8fba65e-dcea-4490-abfd-35ee4ff5053e)
### PhoneCollection After indexing
![af_index_gender](https://github.com/user-attachments/assets/5cba4f31-ed2b-4ede-9ab4-cd37f2d75337)
### Before Deleting Employee
![bf_del](https://github.com/user-attachments/assets/f02ab708-cb74-400d-b301-e48d932294f2)
### Deleting Employee
![del_terminal](https://github.com/user-attachments/assets/7408b0f5-6f86-4951-8309-9719f70285e0)
### After Deleting Empoyee
![af_del](https://github.com/user-attachments/assets/33d6f76a-3e3f-4434-9811-69c3a99fee9d)
### Count after indexing
![count_af_indexing](https://github.com/user-attachments/assets/ef0a0349-3c28-4544-aabb-fe03e0a46e3b)
### SearchbyColumn
![search1](https://github.com/user-attachments/assets/4721c4af-5a95-4de8-92e7-b1c442f5860b)
![search2](https://github.com/user-attachments/assets/30d421af-fab3-40d5-a9ee-1857b9d02c75)
![search3](https://github.com/user-attachments/assets/45afe562-a364-4991-86d8-be04dbd43abd)
## Groups Count for Departments
![last2](https://github.com/user-attachments/assets/57a8d734-a895-4fa3-a725-38193197ba28)
![last1](https://github.com/user-attachments/assets/2b0598af-8855-48d6-aa88-53ce435ac03c)



