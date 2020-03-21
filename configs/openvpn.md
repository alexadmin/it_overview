# OpenVPN
OS: Centos 7

PKG: openvpn-2.4.8-1.el7.x86_64, openvpn-auth-ldap-2.0.3-17.el7.x86_64

LDAP server: Windows Server 2012 Std

/etc/sysctl.d/forward.conf
```
net.ipv4.ip_forward=1
```

/etc/sysconfig/iptables
```
*nat
:PREROUTING ACCEPT [1073:55509]
:INPUT ACCEPT [42:2096]
:OUTPUT ACCEPT [598:39274]
:POSTROUTING ACCEPT [187:12912]
-A POSTROUTING -s 10.0.0.0/8 -o ens192 -j MASQUERADE
-A POSTROUTING -s 192.168.0.0/24 -o ens192 -j MASQUERADE
COMMIT
```
```
-A FORWARD -s 10.0.0.0/8 -j ACCEPT
-A FORWARD -s 172.16.0.0/12 -j ACCEPT
-A FORWARD -s 192.168.0.0/16 -j ACCEPT
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
```

/etc/openvpn/server.conf
```
port XXXX
proto udp
dev tun
ca keys/ca.crt
cert keys/server.crt
key keys/server.key
dh keys/dh.pem
mode server
tls-server
tls-auth keys/ta.key 0
server XXXXXXXX 255.255.255.0
ifconfig-pool-persist ipp.txt
username-as-common-name
tun-mtu 1400
script-security 2
client-to-client
#auth MD5
daemon
cipher BF-CBC
keepalive 10 120
comp-lzo
max-clients 100
persist-key
persist-tun
status /var/log/openvpn/openvpn-status.log
log /var/log/openvpn/openvpn.log
verb 4
push "dhcp-option DNS XXXXX"
push "dhcp-option DNS XXXXX"
push "route XXXXXX 255.255.0.0"
plugin /usr/lib64/openvpn/plugin/lib/openvpn-auth-ldap.so auth/ldap.conf
```
/etc/openvpn/auth/ldap.conf
```
<LDAP>
        URL             ldap://XXXXXXX
        BindDN          XXXXXXXXXXXXXXX
        Password        XXXX
        Timeout         15
        TLSEnable       no
        FollowReferrals yes
</LDAP>

<Authorization>
       BaseDN          "XXXXXXXX"
       SearchFilter    "(&(objectCategory=person)(objectClass=user)(sAMAccountName=%u)(memberOf=CN=XXXXXXX))"
</Authorization>
```

client.conf
```
client
proto udp
dev tun
#
remote XXXXXXXXX XXXX
remote XXXXXXXXX XXXX
remote XXXXXXXXX XXXX
connect-timeout 5
#
dhcp-option DNS XXXXXXXX
dhcp-option DNS XXXXXXXX
#
resolv-retry infinite
nobind
auth-user-pass
comp-lzo
ca ca.crt
cert client.crt
key client.key
dh dh2048.pem
tls-client
tls-auth ta.key 1
float
keepalive 10 120
persist-key
persist-tun
verb 0
```
