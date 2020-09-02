shell
=========

**shell scripting**

https://en.terminalroot.com.br/45-examples-of-variables-and-arrays-in-shell-script/



**debugging**

 `set -ex` падать при первом возврате не 0 команды

 `set -x`  display the expanded trace before running the command

 `set -v`  display the input line as it is parsed


**Parameter expansion**

    https://wiki.bash-hackers.org/syntax/pe

**creating binary from sh script**  

    https://linux.die.net/man/1/shc
    
**convert codepage**

    recode -f /QP..utf8
    
 **remove double quotes**
 
    echo "$opt" | sed -e 's/^"//' -e 's/"$//'
    B=$(tr --delete '\"' <<< $A)
    
  

**if brackets comparing**

[ What is the difference between test, `[ and [[` ](http://mywiki.wooledge.org/BashFAQ/031)

https://linuxize.com/post/how-to-compare-strings-in-bash/
```
    [ is the same as the test builtin, and works like the test binary (man test)
        works about the same as [ in all the other sh-based shells in many UNIX-like environments
        only supports a single condition. Multiple tests with the bash && and || operators must be in separate brackets.
        doesn't natively support a 'not' operator. To invert a condition, use a ! outside the first bracket to use the shell's facility for inverting command return values.
        == and != are literal string comparisons
    [[ is a bash
        is bash-specific, though others shells may have implemented similar constructs. Don't expect it in an old-school UNIX sh.
        == and != apply bash pattern matching rules, see "Pattern Matching" in man bash
        has a =~ regex match operator
        allows use of parentheses and the !, &&, and || logical operators within the brackets to combine subexpressions
```


Aside from that, they're pretty similar -- most individual tests work identically between them, things only get interesting when you need to combine different tests with logical AND/OR/NOT operations.
