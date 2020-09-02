bash
====

| hotkey |action | 	
|---|---|
**Navigation**
|Ctrl + a	| Go to the beginning of the line|
|Ctrl + e	| 	Go to the end of the line|
|Alt + f		| Move the cursor forward one word|
|Alt + b		| Move the cursor back one word|
|Ctrl + f		| Move the cursor forward one character|
|Ctrl + b		| Move the cursor back one character|
|Ctrl + x, x		| Toggle between the current cursor position and the beginning of the line|
**Editing**
|Ctrl + _		| Undo|
|Ctrl + x, Ctrl + e		| Edit the current command in your $EDITOR|
|Alt + d		| Delete the word after the cursor|
|Alt + Delete		| Delete the word before the cursor|
|Ctrl + d	| Delete the character beneath the cursor|
|Ctrl + h	| Delete the character before the cursor (like backspace)|
|Ctrl + k	| Cut the line after the cursor to the clipboard|
|Ctrl + u	| Cut the line before the cursor to the clipboard|
|Ctrl + d	| Cut the word after the cursor to the clipboard|
|Ctrl + w | Cut the word before the cursor to the clipboard|
|Ctrl + y	| Paste the last item to be cut.|
|Alt+T |	Swap Current Word with Previous|
|Ctrl+T 	|Swap Last Character before Cursor|
|Esc+T 	|Swap Last Two Words Before Cursor|
|Alt+U 	|Upper Capitalize Every Character form Cursor|
|Alt+L 	|Lower The Case Every Character Form Cursor|
|Alt+C 	|Capitalize Character Under Cursor and Move to End of the Word|
|Alt+R |	Cancel Changes and Put Back the Line|
|Ctrl+I 	|Tab|
|Ctrl+J 	|NewLine|
|Ctrl+M |	Enter|
|Ctrl+[ |	Escape|
**Processes**
|Ctrl + l	| Clear the entire screen (like the clear command)|
|Ctrl + z	| Place the currently running process into a suspended background process (and then use fg to restore it)|
|Ctrl + c	| Kill the currently running process by sending the SIGINT signal|
|Ctrl + d	| Exit the current shell|
|Ctrl+S 	| Stop Output to Screen|
|Ctrl+Q |	Allow Output to Screen|
|Enter, ~, .	| Exit a stalled SSH session|
**History**
|Ctrl + r	| Bring up the history search|
|Ctrl + g	| Exit the history search|
|Ctrl + p	| See the previous command in the history|
|Ctrl + n	| See the next command in the history|
|Ctrl+S |	Go back to Next Most Recent Command|
|Ctrl+O |	Execute Command found via Ctrl+R/Ctrl+S|
|!! 	| Repeat Last Command|
|!abc |	Run Last Commnd Starting with abc|
|!abc:p |	Print last Command starting with abc|
|!$ |	Last Argument of Previous Command|
|Alt+. |	Last Argument of Previous Command|
|!* |	All Arguments of Previous Command|
|^abc^def |	Run Previous Command,replacing abc with def|
**Emacs Mode/Vi Mode**
|$set -o vi |	Set Vi Mode|
|$set -o emacs |	Set Emacs Mode|
