fastopen
========

https://github.com/yuryu/tfoecho/

**You need to have linux kernel at least 3.7**

 $ vim /etc/sysctl.conf 
   
       net.ipv4.tcp_fastopen = 3
    
 $ sysctl -p



$ vim /etc/rc.local

    echo 3 > /proc/sys/net/ipv4/tcp_fastopen

**check**

$ ip tcp_metrics

$ grep '^TcpExt:' /proc/net/netstat | cut -d ' ' -f 87-92 | column -t

(c) <https://blog.wasin.io/blog/2016/12/26/how-to-enable-fast-tcp-open-on-ubuntu.html>

