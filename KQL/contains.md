## contains

'contains' gives us flexibility of text, we don't have to worry about the entire text 
Its kind of like * we use in Splunk

```kql
// the result will be Extension, extension, extent & anything related & found at any place in the string
| where subject contains 'Ext'
```
