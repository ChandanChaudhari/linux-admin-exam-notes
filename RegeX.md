#### Regular expression
### Character Class
`[XYZ] [a-c]` - Matches any of the enclosed character. same as `\w` `[abcd]` is same as `[a-d] `We can specify range of charcter by using -, EX `[A-Z]`, But if - appear at start or at last it is taken as literal EX ``[-abcd]`` it will matches to - in "non-profit"

### Negated character class :
`[^xyz] [^a-c]`  Matches anything that is not enclosed in the square brackets. 

Note `^` also indicate beginning of input.

 #### Wildecard : 
 `.`  -	Wildcard: Matches any single character except line terminators: \n, \r, \u2028 or \u2029. For example, /.y/ matches "my" and "ay", but not "yes", in "yes make my day", as there is no character before "y" in "yes". If the dotAll (s) flag is enabled, also matches line terminators. Inside a character class, the dot loses its special meaning and matches a literal dot.

#### Digit Character class escape -
`\d` Matches any digit .Equivalant to `[0-9]` EX- `/\d/` or `/[0-9]/` matches "2"

#### Non-digit character escape :
`\D` matches any non digit character Ex - same as `[^0-9]` matches to "B" or "B2"

#### Word character class escape : 
`\w` matches any alphanumeric chracter from basic alphabet including underscore. equivalant to [A-Za-z0-9_]. `/\w/` matches "a" in "apple", "5" in "$5.13"

#### Non-Word character class escape : 
`\W` matches any non word caracter. same like `[^A-Za-z0-9_]` Ex- Matches "%" in "50%" 

### White space character class escape : 
\s 
 
