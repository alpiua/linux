troubleshoot
============
## Rosources

    yum -y install sysstat
    
###  mpstat  | processor related statistic
### iostat | (CPU) statistics and input/output statistics for devices and partitions
### vmstat | Report virtual memory statistics


## Network

### проверка активных соединений

    ss -t -a -n | grep :443 | grep -i est | wc -l
    
### подсчет соединений с ip-адресами
    netstat -an | grep -E '\:80|\:443'| awk '{print $5}' | grep -Eo '[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}' | sort | uniq -c | sort -n
    
    
    
Как понять, что происходит на сервере
https://habr.com/ru/company/oleg-bunin/blog/319020/


ps axufS