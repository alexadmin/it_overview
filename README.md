[OpenVPN](#openvpn)

# deployment environment

### production
Servers in DC

### UAT (User acceptance testing)
- full copy of production servers with user data

### staging
- full copy of production servers, but DB may be anonymized
- may connect to other production services and data, such as databases
- performance testing
- load testing
- test all the installation/configuration/migration scripts and procedures before they're applied to a production environment

https://en.wikipedia.org/wiki/Deployment_environment

https://softwareengineering.stackexchange.com/questions/355103/whats-the-difference-between-staging-and-uat-environments

### test 
servers for QA
> The purpose of the test environment is to allow human testers to exercise new and changed code via either automated checks or non-automated techniques.
>
> -- <cite>https://en.wikipedia.org/wiki/Deployment_environment</cite>

### dev
servers for developers

### local
developer desktops
> Developer's desktop/workstation
>
> -- <cite>https://en.wikipedia.org/wiki/Deployment_environment</cite>

Environments should be abolutely equals. Even by CPU manufactures, app can run in test env, but will fail in prod because of some bug in libC cooperation with kernel. 

# networks

Create separate network for each segment. For example if you have rack in DC and DC provider provide LAN to cloud, keep rack servers and cloud VM in different network to avoid loop. If you aimed to configure VLAN from HQ to DC you will get loop with high probability.

# OpenVPN
OS: Centos 7

PKG: openvpn-2.4.8-1.el7.x86_64, openvpn-auth-ldap-2.0.3-17.el7.x86_64

LDAP: Windows Server 2012 Std

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
port 443
proto tcp
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
proto tcp
dev tun
#
remote XXXXXXXXX 443
remote XXXXXXXXX 443
remote XXXXXXXXX 443
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
