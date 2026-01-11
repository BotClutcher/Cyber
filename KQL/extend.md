## extend
To create/create a new field that dosen't already exist

```kql
// GB = name of new field we want to make

table
| extend GB=Quantity/1000
```
