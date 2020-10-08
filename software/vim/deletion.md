deletion
========================

To remove all lines containing a particular string in the vi or Vim text editors, you can use the g command to globally search for the specified string and then, by putting a "d" at the end of the command line, specify that you want all lines containing the specified string deleted. E.g., If I wanted to remove all lines containing the string "dog", I could use the following command.

:g/dog/d

That command would also remove any lines containing "dogs", "dogged", etc. If I just wanted to remove lines containing "dog", I could use :g/dog /d.

You can, of course, specify the pattern on which you wish to search using regular expressions. E.g., if I wanted to remove any lines containing either "dog" or "hog", I could use the command below.

g/[dh]og/d

By putting the leters "d" and "h" within brackets, I indicate to vi that it should remove any line that has either a "d" or an "h" followed by "og".

If I wanted to remove all comment lines from a script, I could search for any lines beginning with the pound/hash/number sign character. E.g.:

:g/^#/d

The caret indicates that what follows must be at the beginning of a line (the dollar sign indicates that what precedes the dollar sign must occur at the end of the line).

If you want to delete any lines containing one of several specified words, you can separate the words with a vertical bar, aka the pipe character (it's the character you get when you hit the shift key and the backslash character simultaneously, at least on English language keyboards. The vertical bar signifies a "logical or" function should be performed. E.g., if I wanted to delete any lines containing the words "cat" or "dog", I could use the command shown below.

:g/dog\|cat/d

I need to precede the vertical bar with an escape character, i.e., a backslash, to "escape" the special meaning of the vertical bar, which is often used to "pipe" the output of one command into another.

If I wanted to perform a "logical and" function, e.g., to delete any lines containing both the words "cat" and "dog", I could use the command below.

:g/dog.*cat\|cat.*dog/d

The dot character indicates any character can occur in the specified position. The asterisk represents a quantifier indicating zero or more occurrences of the preceding element. So ".*" means zero or more characters can occur between "dog" and "cat". But I also have to put in the vertical bar followed by cat.*dog to address cases where both words occur on the line but "cat" precedes "dog" rather than following "dog".

If I wanted to delete all lines except those that contained a particular string, I could put an exclamation mark after the g command. The exclamation mark signifies a "logical NOT" operation, i.e., negation. E.g., to delete all lines not containing "dog", I could use the command below.

:g!/dog/d

If, instead, I wanted to remove all lines except those containing the words "cat" or "dog", I could use the command below:

:g!/dog\|cat/d

You can specify a range of lines over which the command should operate by preceding the command with the line range. E.g., if I only wanted to delete lines containing the word "dog" for the first five lines, I could use the command below.

:1,5 g/dog/d

Or to apply the command to lines 100 to the last line, I could use the command below, since the dollar sign indicates the last line.

:100,$ g/dog/d


http://support.moonpoint.com/software/editors/vi/remove-lines-string.php
