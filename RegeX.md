#### Regular expression
### Character Class

`[XYZ] [a-c]` - #### Matches any of the enclosed character. `[abcd]` is same as `[a-d] `We can specify range of charcter by using -, EX `[A-Z]`, But if - appear at start or at last it is taken as literal EX ``[-abcd]`` it will matches to - in "non-profit"

`[^xyz] [^a-c]` #### Negated character class : Matches anything that is not enclosed in the square brackets. 

Note `^` also indicate beginning of input.

 Wildecard : `.`  -	#### Wildcard: Matches any single character except line terminators: \n, \r, \u2028 or \u2029. For example, /.y/ matches "my" and "ay", but not "yes", in "yes make my day", as there is no character before "y" in "yes". If the dotAll (s) flag is enabled, also matches line terminators. Inside a character class, the dot loses its special meaning and matches a literal dot.

`\d` ### Digit Character class escape - Matches any digit .Equivalant to `[0-9]` EX- `/\d/` or `/[0-9]/` matches "2"

 `\D ` ### Non-digit character escape : matches any non digit character Ex - same as `[^0-9]` matches to "B" or "B2"


 
