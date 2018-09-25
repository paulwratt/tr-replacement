# tr-replacement
A replacement for `tr`. A superset of coreutils `tr` "a filter to translate characters". One with RegEx.

# Compatibility
The commands `tr2` and `tr3` will always be a superset of `tr` which means it is a drop-in replacement at the (console) command level _AND_ the source level. If you build `tr2` yourself, you can substitute `tr` with a link to `tr2`, or replace `tr` command (eg. `/usr/bin/tr`).

If you are building `coreutils` package you can replace `src/tr.c` with the renamed file `tr2.c`, or `tr3.c` if you always want regular expressions available, which will then build as `tr` command.

# Detecting
You will always be able to tell the difference be an original `tr` and one replaced with `tr2`, as `-2` switch will not return an error code. You can always test for the presence of `/usr/bin/tr2` or `tr -2`.

I you want to detect the presence of regular expressions, you can use `tr -3`. This will be (again) a superset of `tr2`, in `tr3.c`. This just allows system admins to decide just how much _bloat_ they want with there `coreutils`. On some systems it does not make sense to run a `tr` command that requires extra resources needed for regular expressions.

# Differences
There is no difference, it is a superset, so all switches available in `tr` are also available in `tr2`. Ok, so what is this _superset_ of switches:
```
-2              errors if not tr2/tr3 (has tr extensions)
-3              errors if not tr3 (has regular expressions)
-a <n>[,<m>]    only action on characters.
-n <n>[,<m>]    number of replacements.
-m <n>[,<m>]    begin and end of replacements.
-o <n>[,<m>]    omit characters before input.
-y <n>[,<m>]    yeild characters after output.
-V [<char>]     only output that which does not get changed, optionally use <char> for those that have changed
-S <string>     substitutue <string> for replaced characters instead of SET2
-Z              use repeating "background" replacement of <string> with `-S` instead of SET2
-R              reverse initial input string
-r              reverse final output string
```
The switches with `n` and `m` work the same as `{n,m}` does in regular expresions, so:  
`tr2 -n 5 -d a   ` will stop deleting charcater `a` after the 5th match  
`tr2 -n 2,5 a b  ` will replace at least 2, but not more than 5 characters `a` with character `b`  
`tr2 -m 5 -d B   ` will delete only from the 5th match of characters `a` with character `A`  
`tr2 -m 2,5 a A  ` will replace only from the 2nd to the 5th match of characters `a` with character `A`  

However the switches with `n` and `m` also have a `-` action not found in regular expresions, so:  
`tr2 -n -5 -d a  ` will only delete the last 5 matches of charcater `a`
`tr2 -n 2,-5 a B ` will replace the last 5 matches, but stop after 2 matches of characters `a` with character `B`  
`tr2 -n -7,5 a b ` will replace the last 7 matches, but not more than 5 characters `a` with character `b`  
`tr2 -m -5,-2 a A` will replace only from the last 5 matches to the last 2nd match of characters `a` with character `A`  

Also with `n` and `m` you can use `[:even:]` and `[:odd:]` to manipulate the switch input and output.

# RegEx
The command `tr3` takes `tr2` and adds `grep` compatible regular expression command line switches:
```
-2              errors if not tr2/tr3 (has tr extensions)
-3              errors if not tr3 (has regular expressions)
-e <pattern>    action only on regular expression pattern found
-E              pattern is an extended regular expression (ERE)
-F              pattern is a set of newline seperated fixed strings
-G              pattern is an basic regular expression (BRE)
-P              pattern is an Perl regular expression (PCRE)
-f <filename>   get pattern from file
-i              ignore case
-v              invert pattern match
-w              force pattern to macth a whole word
-x              force pattern to match a whole line
-z              a line ends with 0 byte, not newline
-A              match the pattern after everything else
-B              match the pattern before everything else
-D              dont drop line output if no match found (SED style)
```

