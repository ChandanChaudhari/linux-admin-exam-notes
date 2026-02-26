#### Regular expression
### Character Classe

`[XYZ] [a-c]` - #### Matches any of the enclosed character.We can specify range of charcter by using -, EX `[A-Z]`, But if - appear at start or at last it is taken as literal EX ``[-abcd]`` it will matches to - in "non-profit"


`.`  -	#### Wildcard: Matches any single character except line terminators: \n, \r, \u2028 or \u2029. For example, /.y/ matches "my" and "ay", but not "yes", in "yes make my day", as there is no character before "y" in "yes". If the dotAll (s) flag is enabled, also matches line terminators. Inside a character class, the dot loses its special meaning and matches a literal dot.

