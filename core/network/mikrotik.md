mikrotik
========

MikroTik DNS block

DUDE for mikrotik

DNS based adblock using Mikrotik RouterOS – Paul Bryan Vreeland 
https://paul.is-a-geek.org/2018/02/dns-based-adblock-using-mikrotik-routeros/

**Перезапуск по питанию USB**
```
/interface ppp-client monitor ppp-3g once do={:if ($status != "connected") do= {:log warning  "PPP Link Down, reset USB"; /system routerboard usb power-reset duration=30}}
```

расширенный:

`/interface ppp-client monitor ppp-out1 once do={:if ($status != "connected") do= {:log warning  "PPP Link Down (#1)"}}; :delay 30;  /interface ppp-client monitor ppp-out1 once do={:if ($status != "connected") do= {:log warning "PPP Link Down (#2)"}}; :delay 30; /interface ppp-client monitor ppp-out1 once do={:if ($status != "connected") do={:log warning "PPP Link Down, Resetting USB Port"; /system routerboard usb power-reset duration=4}}; :delay 120; /interface ppp-client monitor ppp-out1 once do={:if ($status = "connected") do= { /system script run afraiddnsupdate;}}`
