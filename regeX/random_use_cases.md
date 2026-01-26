
## Find the email address 

```kql
// as per GPT
   ^[a-zA-Z0-9._+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$

// as per my supercomputer brain 
[a-zA-Z0-9._+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]+
```
## Find IP address

```kql

// using \ as without it there could be errors
[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+

```
## Find linux Dir

```kql
\/[a-z0-9]+\/[a-z0-9]+\/[a-z0-9]+

// like
// /var/log, /var/log/apache1, /var/log/

// Mr GPT is like, (bastard is better in this case too, huh)
/[a-z0-9]+(/[a-z0-9]+)+

```
