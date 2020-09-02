Xephyr
======

**Nested X server** 
Full screen video or game inside the i3 window. For example - two separate Firefox windows, one inside the Xephyr window, and the other one normally on a different workspace for actual full screen.

<https://en.wikipedia.org/wiki/Xephyr>

**To get everything running:**
1. open a spare terminal

2. Type  
`Xephyr -ac -resizeable -br -reset -terminate 2> /dev/null :1 &`  
This should open a new, black window, which you can move around and resize like any other window in i3.  

3. Then, run `DISPLAY=:1.0`  
to make sure subsequent x windows started from this terminal open in the Xephyr window.  

4. Finally, just run `firefox`
