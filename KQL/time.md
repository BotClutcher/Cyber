## time
 this place have few of the use cases of time

```kql
// d = day
// h = hour
// m = min
// s = sec

| where TimeGenerated >= ago(7d)

// this will be time till midnight
| where TimeGenerated between (2023-7-5) .. datetime(2023-7-4)

| between (datetime(2023-7-3) .. now())
| between (datetime(2023-7-3) .. (now() - 4h))

```
