📖 This is the place where I wll be documenting all of my learning related to KQL. 

The resources I am using are YouTube Videos by 10 Min KQL & official Microsoft Learn platform. 

---

SYNTAX
```kql
customer                     // table name
| where FirstName == 'Peter'  // field name & field value
| take 10                      // show random 10 values, good when we want to have a lay of the land
                                // we can replace it with limit 10
```

Few notes beforehand, 
- KQL is **CASE SENSITIVE**
- No issues with spacing generally,
- // is for commenting
- ' ' or " " can both work
- order of | matters a lot 

== : Exact Match

!= : Does Not match
  - !has
  - !contain
  - !startswith
  - !endswith
  - !startswith_cs
=~ Case Insensitivity
