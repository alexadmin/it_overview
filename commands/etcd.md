```
# SHOW VERSION
curl -L http://127.0.0.1:2379/version

# CHECK CLUSTER HEALTH
etcdctl cluster-health

# SHOW MEMBERS
etcdctl member list

# LIST
etcdctl get /coreos.com/network/ --prefix --keys-only

# BACKUP 
etcdctl backup --data-dir /var/lib/etcd/default.etcd/member/ --backup-dir etcd_backup
```
