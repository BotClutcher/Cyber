## Repeaters 
There are a few types of repeaters:  
   - **Astrick**(*) - 0+ preceding char, ie, could be 0 or more of the preceding char 
   - **Addition symbol** (+)  - at least more than 0 of preceeding char
   - {} - define a range

```kql
- ab*c: ac, abc, abbc, abbbc ...     // 0+
- ab+c: abc, abbc, abbbc ...        // minimum 1
- ab{2}: abb                        // exactly 2 b
- Go{2, 4}gle: Gooogle, Goooogle, Gooooogle // between 2-4 char or preceding one
```

## Wildcard(.)
Match anything except new line, & is only 1 char & not like repeaters

```kql
a.c - aac, abc, acc, adc, ...

// in repeaters it would be like
a*c - aac, aaac, aaaac ... 
```

## Optional(?)
Make the preceding char optional

```kql
colou?r - color, colour
// interestingly i watched 2 tutorials & in different languages, but both of them used the same examples, Nice 
```

## Position Anchor(^, $)
Caret(^)- find at the START of the line 
        - can also find if line starts with caps using ^[A-Z]
        BUT
        - if used inside [ ] then will skip the mentioned thing
        [^0-9] : anything except numbers
        [^a-z] : anything except lower char
Dollar Sign($)- find at the END of the line 

```kql
^Find
$world
```

## Escape sequence(\)
Treat what is after \ as just a normal char

```kql
\+
\/
```

## Character Class
\s - match all the white spaces
\s+ - for multiple white spaces

\S - match everything except the whitespaces

\d - any digit between 0-9
[0-9]- any digit between 0-9

\D - match any non-digit (anything except 0-9)

\w - match any word char, letter or underscore(_)
[a-z] - match any word char 

\W - match non-alpha numeric stuff (anything except a-z)

| - either or - (cat|dog) - either cat or dog








