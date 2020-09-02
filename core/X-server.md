X-server
========

# VGA - setup second monitor

`cvt X Y`	*calculate settings to use with xrandr*  
`xrandr`	*find name and current settings*


```
xrandr --newmode  "1920x1080_60.00"  173.00 1920 2048 2248 2576 1080 1083 1088 1120 -hsync +vsync

xrandr --addmode VGA-1 "1920x1080_60.00"

xrandr --output LVDS-1 --auto --rotate normal --pos 0x0 --output VGA-1 --mode "1920x1080_60.00" --right-of LVDS-1
```

# Display Port settings

`xrandr --output LVDS-1 --auto --rotate normal --pos 0x0 --output DP-1 --mode "2560x1440" --rate 75 --right-of LVDS-1`

# Turn off second monitor

`xrandr --output DP-1 --off`

# DPI real size

https://habr.com/ru/post/506082/