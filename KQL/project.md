## project
only show stuff/fields/columns we want to see & nothing else

```kql
<table_name>
| project time, Host, CPUs
```


## project-reorder
if we want to re-order our tables & keep all the other fields too

```kql
<tabel>
| project-reorder <field1>, <field2>, <filed3>
```
