nmap
====

**find all active IP adresses in network**

    nmap -sP 192.168.1.0/24; arp-scan --localnet | grep '192.168.1.[0-9]* *ether'        