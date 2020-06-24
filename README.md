# Foreword

In this book I will share with you knoledge of services which was successfully installed in production of different companies. I spent many years with that systems, I spent years of testing it. I'd like than you will not lose years for choosing between different technologies. 

when you've been hired for a job, start with that:

[OpenVPN](configs/openvpn.md)

VPN - tool for technical specialist, not for managers.

[balancer](#balancer)

[webserver](#webserver)

[documentation](#documentation)

[authorization service](#authorization-service)

==================================================

# Services

- connect loosely coupled services with REST, but not with something like RabbitMQ. (I have experience with company and system integrator, one day system integrator updated RabbitMQ, but developer in company was not aware of that. RabbimMQ change message algorithm and company RabbitMQ client become loop)

# Deployment

1. One script must deplpy app to all envs

2. App with paticular version should build one time and accept variable depends on env.

3. One app to one DB.

The most simple scheme: Jenkins + Ansible. Create simple job in Jenkins and run ansible something like that:
```
ansible-playbook -i dev/some_app.yml
ansible-playbook -i prod/some_app.yml
```

> Using the same script to deploy to production that you use to deploy to development environments is a fantastic way to prevent the “it works on my machine” syndrome (1)

> Use the same process to deploy to every environment (1)



# documentation

Documentation shoud be in one place.

Confluence - is good choice. Free mediawiki can't competite with him. 

- links from one page to other pages will change while you will move pages. You will edit your documentation, you will change structure of documents

- provides authorization, not all data should be exposed.

# Docker
- I didn't use docker for databases and balancers

# authorization service

Windows Server - is best choise. Yes, I'm Linux admin, but I use it. OpenLDAP is not suited for commercial exploitation. Install at least 2 servers. First server promote to DC, add second server to domain of first server, promote second server to DC.

# Job processes

Create task, describe it. How you will do it. Count all cooperation people, create agreament with all people. Before production deployment write plan of installation and reverce and backup steps

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
- developer can bring up server on desktop and tell another developer ip address, that way multiple server born. Rescrict on swith that developers can't see each other.

# Security

## SELinux

Jist disable it. It's hell.

# Log server

Graylog is best choice. Was founded in 2009.
- LDAP auth with group mapping
- all transports

# Backup

- even if you backup VM, backup db localy, it provides you to restore db fast. Restore VM is long.
- pg_dumpall - not consistent, use pg_basebackup
- backup dbs each first day of month for 1 year, and of cause everyday during week

# New service deploy
- it shouldn't create technical dept

# balancer
- should be dumd, idea balancer - nginx with one reverse proxy block, proxy next upstream, it shouldn't contain logic, so you can bring up web server in different env, and all should work without balancer. 
- create small VM for stale domains, 301 etc. Do not store legacy domains for redirects in production.
- suited for: ip filtering, auth (basic)

# webserver
- suited for: rewrites and redirects, web site should work without balancer 

# iptables
- tcpdump can see incoming packets if they are droped by iptables, but can't see outgoing

# mount vs synchronization

Mount directory by NFS or samba seems fast and easy solution, but you should not use it in production because of these reasons:

- share can be mounted to multiple servers, some one think about re-installation, he can switch off traffic on nginx upstream and delete contents in shared folder... That person may be complete unaware than it is mount directory. 
- if share becomes inaccessible because of network reasons, whole OS may hung, you can't even check free disk space with ```df -h``` or list some directories and application may crash.
- when application write/read file to remote folder, application thinks that it is local directory, but it is not truth. This folder has network latency and application may crash.
- very difficult troubleshooting. When problems with file synchronization happen you should check 2 things: port accessibility and file system permissions, if something go wrong with NFS, you should troubleshoot whole protocol - https://tools.ietf.org/html/rfc1813, and samba even more robust.
- intelligent write to server. When you configure file synchronization you can limit synchronization bandwidth, it can prevent server overload.

# backup
- if you use rsync run as root on both servers, source server should be completely accessible. For example Bacula documentation: "The Clients (bacula-fd) must run as root to be able to access all the system files."

# ddos
dos - attack from one server
ddos - attack from multiple servers
- system administrators and webservers can block dos attack
- ddos attack can be blocked only by application (for example 2FA)
You can review proxy servers services, they are offers simple tariff plan with 500k ip addresses. Let's say from 500k 1k will be very active, you can detect it fast in your logs, but another ip addressed will request your servers a couple times per day. If you will block 1k active ip addressed it will not resolve the issue.

# monitoring
- every storage service RabbitMQ, MySQL... should be monitored. Script should generate hash, write it, read it and compare it.

- QA engineers should work with GUI only

# monolithic vs microservice

- monolithic is good at start up for small apps
- move from monolithic to microservice when:
  - developers IDE slow down
  - long time to start up
  - modules conflicts with each other

# microservices
- each app has it own database (create database my_app)

# External sources + recomended books

1) Continuous Delivery Reliable Software Releases through Build, Test, and Deployment Automation by Jez Humble, David Farley. 2010

2) "Refactoring Databases" and book "Recipes for Continuous Database Integration". Scott Ambler.
