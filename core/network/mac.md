mac
===

ip link set dev eth0 address XX:XX:XX:XX:XX:XX

**find original mac**

    dmesg | grep eth0

**restore mac address**

    #!/bin/sh
    set_to_real() (
      for i do
        mac=$(ethtool -P "$i" | grep -iEom1 '([0-9a-f]{2}:){5}[0-9a-f]{2}') &&
          ip link set dev "$i" address "$mac"
      done
    )
    set_real eth0