```
# redirect port
iptables -t nat -A PREROUTING -i ens192 -p tcp --dport 5432 -j REDIRECT --to-ports 5430
```
