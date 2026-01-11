## search
search openly the table &/or all the fields

```kql
| search "*Azure"
| search "Azure*"

| search Location: "FR"

// say we are looking for something & don't know which table to look at
// this will consume a lot of resources
search "40.192.168.1"

// look for all the tables with this IP
search "10.182.181.11"
| distinct $table

// to look in specific tables only
search in (table1, table2) "google"
```
