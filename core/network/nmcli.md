nmcli
========================
mncli con show eth0
nmcli con mod eth0 ipv4.addresses 192.168.1.2/24
nmcli con mod eth0 ipv4.gateway 192.168.1.1
nmcli con mod eth0 ipv4.dns "1.1.1.1 8.8.8.8"
nmcli con mod eth0 ipv4.dns-searc "1.1.1.1 8.8.8.8"


nmcli c d eth0; nmcli c u eth0
