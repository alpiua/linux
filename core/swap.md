swap
====

```
dd if=/dev/zero of=/swapfile bs=1M count=4096 && \
chmod 600 /swapfile && \
mkswap /swapfile && \
swapon /swapfile && \
echo "/swapfile none swap defaults 0 0" | tee -a /etc/fstab
```

<https://habr.com/ru/company/flant/blog/348324/>