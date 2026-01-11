## has

has looks for full strings but can use delimeters(/ , . [ ] {) & is not case sensitive
**focuses on the string**
however, _contains_ will pick anything related, & located anywhere in the string

```kql
| where user_agent has "Firefox"
```

if we want to look for URL start with http, then prefer **has**

```kql
| where url has "http" 
```

```kql
// search the entire table
| where * has 389
```

