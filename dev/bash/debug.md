debug
=====

The verbose and echo variables in the C shell

The C shell also has two variables that, when set, will help you follow the convoluted trail of variable and meta-character expansion. The command

    set verbose

will echo every line of your script before the variables have been evaluated. The command

    set echo

will display each line after the variables and meta-characters have been substituted. If you wish to turn the variables off, use unset instead of set

A convenient way to turn these variables on the first line of the script using the the "-x" option (echo)

    #!/bin/csh -x

or the "-v" option (verbose):

    #!/bin/csh -v

In both examples above, the .cshrc file is read at the beginning of the script. The "-f" option can skip this file. You can combine all three options if you like:

    #!/bin/csh -fxv

If you want to read in the .cshrc file, and want to trace the values of these variables, capitalize the "X" and "V" variables. This turns on tracing before the .cshrc file is read:

    #!/bin/csh -XV

It is not necessary to modify the program if you want to turn on the verbose or echo variables. If this is a script that you do not have the permissions to modify, you can set these variables from the command line:

    % csh -x shell_script

