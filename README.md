# Foreword

In this book I will share with you knoledge of services which was successfully installed in production of different companies. I spent many years with that systems, I spent years of testing it. I'd like than you will not lose years for choosing between different technologies. 

when you've been hired for a job, start with that:

[OpenVPN](configs/openvpn.md)

[documentation](#documentation)

[authorization service](#authorization-service)

==================================================

# Deployment

The most simple scheme: Jenkins + Ansible. Create simple job in Jenkins and run ansible something like that:
```
ansible-playbook -i dev/some_app.yml
ansible-playbook -i prod/some_app.yml
```

```
Using the same script to deploy to production that you use to deploy to development environments is a fantastic way to prevent the “it works on my machine” syndrome
```
Continuous Delivery Reliable Software Releases through Build, Test, and Deployment Automation by Jez Humble, David Farley

# documentation

Documentation shoud be in one place.

Confluence - is good choice. Free mediawiki can't competite with him. 

- links from one page to other pages will change while you will move pages. You will edit your documentation, you will change structure of documents

- provides authorization, not all data should be exposed.

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

# Security

## SELinux

Jist disable it. It's hell.

# Log server

Graylog is best choice. Was founded in 2009.
- LDAP auth with group mapping
- all transports

# Databases

- pg_dumpall - not consistent, use pg_basebackup
- backup dbs each first day of month for 1 year, and of cause everyday during week
