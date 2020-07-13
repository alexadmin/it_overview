```
# PAGE LOAD TIME
time wget -pq --no-cache --delete-after http://www.alexlinux.com
```

# SHOW JITTER
iperf3 -c alexlinux.com -u -J | jq '.end.sum.jitter_ms'

# SHOW TCP SESSIONS
lsof -i tcp

swarm
```
[bash]
# REBALANCE REPLICA BW NODES
docker service update --force SERVICENAME

# CREATE SERVICE IN SWARM
docker service create --name my_web --replicas 1 nginx

# RESTART CONTAINER
docker service scale myserv=0
docker service scale myserv=1

# check traffic on swarm worker
tcpdump -nn -i eth0 not port 22 and not port 7946 and not port 4789 and not port 2377
```

# DUMP UNIX DOMAIN SOCKET
socat -t100 -x -v UNIX-LISTEN:/run/alex.socket,mode=777,reuseaddr,fork UNIX-CONNECT:/var/lib/mysql/mysql.sock
