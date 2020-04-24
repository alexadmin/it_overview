```
# SHOW VERSION
curl -L http://127.0.0.1:2379/version

# CHECK CLUSTER HEALTH
etcdctl cluster-health

# SHOW MEMBERS
etcdctl member list

# SHOW LEADER
# ALL HOSTS
etcdctl -w table --endpoints=[ip]:2379,[ip]:2379,[ip]:2379 endpoint status
# SINGLE HOST
etcdctl -w table endpoint status

# LIST
etcdctl get /coreos.com/network/ --prefix --keys-only

# BACKUP 
etcdctl backup --data-dir /var/lib/etcd/default.etcd/member/ --backup-dir etcd_backup
```

# installation
```
# INSTALL DAEMON ON EACH HOST, IN MY CASE WILL BE 3 NODES
# IN MY CASE IT IS http://cbs.centos.org/repos/virt7-docker-common-release/x86_64/os/Packages/etcd-2.0.9-1.el7.x86_64.rpm
yum -y install etcd
```

<strong>etcd 1</strong>

```
cat /etc/etcd/etcd.conf
# [member]
ETCD_NAME=etcd1
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://10.99.1.1:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.99.1.1:2379,http://127.0.0.1:2379"
#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.99.1.1:2380"
ETCD_INITIAL_CLUSTER="etcd1=http://10.99.1.1:2380,etcd2=http://10.99.1.2:2380,etcd3=http://10.99.1.3:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="ab5f20b33aa4"
ETCD_ADVERTISE_CLIENT_URLS="http://10.99.1.1:2379"
```

<strong>etcd 2</strong>

```
cat /etc/etcd/etcd.conf
# [member]
ETCD_NAME=etcd2
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://10.99.1.2:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.99.1.2:2379,http://127.0.0.1:2379"
#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.99.1.2:2380"
ETCD_INITIAL_CLUSTER="etcd1=http://10.99.1.1:2380,etcd2=http://10.99.1.2:2380,etcd3=http://10.99.1.3:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="ab5f20b33aa4"
ETCD_ADVERTISE_CLIENT_URLS="http://10.99.1.2:2379"
```

<strong>etcd 3</strong>

```
cat /etc/etcd/etcd.conf
# [member]
ETCD_NAME=etcd1
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://10.99.1.3:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.99.1.3:2379,http://127.0.0.1:2379"
#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.99.1.3:2380"
ETCD_INITIAL_CLUSTER="etcd1=http://10.99.1.1:2380,etcd2=http://10.99.1.2:2380,etcd3=http://10.99.1.3:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="ab5f20b33aa4"
ETCD_ADVERTISE_CLIENT_URLS="http://10.99.1.3:2379"
```


```
# ON EACH HOST START DAEMON
systemctl start etcd; systemctl enable etcd

# CHANGE CONF AFTER FIRST START
sed -i s'/ETCD_INITIAL_CLUSTER_STATE="new"/ETCD_INITIAL_CLUSTER_STATE="existing"/'g /etc/etcd/etcd.conf
```

<strong>etcd: already initialized as member before, starting as etcd member</strong>

<strong>CLEAN DATA</strong>
```
===========================
# systemctl restart etcd
# rm -rf /var/lib/etcd/*
# systemctl status -l etcd
===========================
```

<strong>REINSTALL NODE STEPS</strong>
```
===========================================================
# REMOVE NODE FROM WORKING SERVER
etcdctl member remove xxx

# ON NEW NODE
# 0) install etcd
# 1) insert old config
# 2) set ETCD_INITIAL_CLUSTER_STATE="new" in etcd.conf
# 3) start etcd
# 4) add member from working node
# 5) stop etcd
# 6) rm -rf /var/lib/etcd/*
# 7) set ETCD_INITIAL_CLUSTER_STATE="existing" in etcd.conf
# 8) start etcd
===========================================================
```
