1. One script must deploy app to all envs.

2. App with paticular version should build one time and accept variable depends on env. 


4. All connections and parameters to external sources should be in one file.



Testers should write automated test before developers starts write code.

[Foreword](#Foreword)

[Documentation](#Documentation)

[Continuous delivery](#Continuous-delivery)

[Deployment](#Deployment)

[All services](#All-services)

[Infrastrusture services](#Infrastrusture-services)

[Application services](#Application-services)

[Testing](#Testing)

[Managment](#Managment)

[Hardware](#Hardware)

[Deployment environment](#Deployment-environment)

# Foreword

In this book I will share with you knoledge of services which was successfully installed in production of different companies. I spent many years with that systems, I spent years of testing it. I'd like than you will not lose years for choosing between different technologies. 

# Ideology
- KISS
- Things that are complex are not useful. Things that are useful are simple. M. Kalashnikov.
- system can't work without: feedback, audit.

# Managment
- 100% people utilization are equals to 100% utilization of server. [4] p. 11. 
- project failure occured because of people misunderstanding, not technocal (1, p. 279)
- developers administer own desktop
- single ticket system
- after meetings with decision, fix result in email with all participants. Otherwise after some time people forget plan and tell gibberish.
> We believe very strongly that the most effective teams develop software in cross-functional groups that are composed of people from all of the different disciplines required to define, develop, test, and release software. These groups should sit together—when they don’t, they don’t benefit from each other’s knowledge. (1, p. 439)
- "SREs are engineers" (8, p. VX)
- "SRE is what happens when you ask a software engineer to design an operations team" (8, p. 5)
- "Google’s rule of thumb is that an SRE team must spend the remaining 50% of its time actually doing development." (8, p. 6)
- "SRE team is responsible for the availability, latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning of their service(s)" (8, p. 7)

# Continuous delivery

Continuous delivery based on ITIL 
> In contrast, this book assumes that you already have a strategy in place, along with processes for managing it. (1, p. 421)


# Deployment
- One script must deploy app to all envs.
- Practices such as using the same process to deploy into every environment and automated environment, data, and infrastructure management are designed to ensure that the release process is thoroughly tested, the possibility for human error is minimized. (1, p. 418)
- App with paticular version should build one time and accept variable depends on env.
- one app restricted to one DB (1, p. 329)
- production and staging environment should contain the same structure. (1, p.54)
- using the same script to deploy to production that you use to deploy to development environments is a fantastic way to prevent the “it works on my machine” syndrome (1)
- deployment to production and testing shoud deferent only by configuration files, process must be the same (1, p. 249)
- user should see list of build versions, current running version and button "deploy" (1, p. 249)
- even non technical user can deploy application back and forth (1, p. 255)
- build files should be compile once and upload to artifact server, they should not be recompiled every time (1, p. 256)
- configs should be applied while deployment (1, p. 257)
- release should take a couple of minutes (1, p. 280)
- create table "version" in database and increment/decrement version, application should compare own version in db version (1, p. 328)
- migration roll-back script copy data to temp table before delete column, and copy back when necessary (1, p. 329)
- in hardcode monolith it is better to record app to db table links (1, p. 331)
- during deploy read DB tables and parse code, you can which table some app uses (1, p. 331)
- blue green DB: clone DB, apply migrations, switch traffic (1, p. 332)
- New service like new baby, think before creation. "40–90% of the total costs of a system are incurred after birth" (8, p. XV)

### Database migrations

- The same process should be used whether deploying the application to a development or acceptance testing environment with a minimal dataset, or whether migrating the production dataset as part of a deployment to production. (1, p. 343)

##### Microservices

- Saga pattern suited for microservices. It is local transaction directed by message broker.

# Documentation

Documentation shoud be in one place. Documentation should act as caching proxy, if developer write code without documentation, that developer will work like caching proxy, he will be DDOSed by other developers or managers.

Confluence - is good choice. Free mediawiki can't competite with him. 

- links from one page to other pages will change while you will move pages. You will edit your documentation, you will change structure of documents

- provides authorization, not all data should be exposed.

# All services

- production servers should be created from scratch (without data) by configuration managment. (1, p.54)
- do not make changes on production directly (1, p. 273)
- in small company you can control everything, is it briliant (1, p. 285)
- keep infrastructure under git-like, do not make changes without approval (1, p. 285, p. 287)
- update process should be managed by single ticket system (1, p. 287)
- history of changes is necessary (1, p. 287)
- automation over documentation (1, p. 287)

# Infrastrusture services

### Kubernetes

In small and middle envs install 2 instances of simple installation in vitrual machines. Create 2 VM, install OS, create snapshots "clean_os", then install k8s

```
kubeadm init --pod-network-cidr=10.244.0.0/16
kubeadm init --pod-network-cidr=10.244.0.0/16
````

Create worker servers, join to masters. In case of diaster rollback to "clean_os", delete all docker worker containers, reinstall k8s, redeploy all services.  

### authorization service

Windows Server - is best choise. Yes, I'm Linux admin, but I use it. OpenLDAP is not suited for commercial exploitation. Install at least 2 servers. First server promote to DC, add second server to domain of first server, promote second server to DC.

### mailing

### log server

Graylog is best choice. Was founded in 2009.
- LDAP auth with group mapping
- all transports

### backup

- even if you backup VM, backup db localy, it provides you to restore db fast. Restore VM is long.
- pg_dumpall - not consistent, use pg_basebackup
- backup dbs each first day of month for 1 year, and of cause everyday during week

### networks

Create separate network for each segment. For example if you have rack in DC and DC provider provide LAN to cloud, keep rack servers and cloud VM in different network to avoid loop. If you aimed to configure VLAN from HQ to DC you will get loop with high probability.
- developer can bring up server on desktop and tell another developer ip address, that way multiple server born. Rescrict on swith that developers can't see each other.

### iptables
- tcpdump can see incoming packets if they are droped by iptables, but can't see outgoing

### backup
- if you use rsync run as root on both servers, source server should be completely accessible. For example Bacula documentation: "The Clients (bacula-fd) must run as root to be able to access all the system files."

### Docker
- I didn't use docker for databases and balancers

### balancer
- should be dumd, idea balancer - nginx with one reverse proxy block, proxy next upstream, it shouldn't contain logic, so you can bring up web server in different env, and all should work without balancer. 
- create small VM for stale domains, 301 etc. Do not store legacy domains for redirects in production.
- suited for: ip filtering, auth (basic)
- usually not necessary for test env, but sometimes for example you need to test payment services for mobile devices, they required for https.

### webserver
- suited for: rewrites and redirects, web site should work without balancer 
- not necessary https, it's impossible to debug/snif trafic

### VPN

VPN - tool for technical specialist, not for managers.

[OpenVPN](configs/openvpn.md)

# Application services

- connect loosely coupled services with REST, but not with something like RabbitMQ. (I have experience with company and system integrator, one day system integrator updated RabbitMQ, but developer in company was not aware of that. RabbimMQ change message algorithm and company RabbitMQ client become loop)

### microservices

- migration from monolith to microservice: first step - create new features in microservice. The goal is to stop monolith growing. (3, p. 433)
- use microservice chassis

### databases

- do not permit devs to create databases in production, staging. They can brake overview logic.

# Job processes

- Create task, describe it. How you will do it. Count all cooperation people, create agreament with all people. Before production deployment write plan of installation and reverce and backup steps
- QA (junior, manual) engineers should work with GUI only

# Deployment environment

### production
Servers in DC

### UAT (User acceptance testing)
- full copy of production servers with user data
- for tesing users or item sorting

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

# Security

## SELinux

Jist disable it. It's hell.

# mount vs synchronization

Mount directory by NFS or samba seems fast and easy solution, but you should not use it in production because of these reasons:

- share can be mounted to multiple servers, some one think about re-installation, he can switch off traffic on nginx upstream and delete contents in shared folder... That person may be complete unaware than it is mount directory. 
- if share becomes inaccessible because of network reasons, whole OS may hung, you can't even check free disk space with ```df -h``` or list some directories and application may crash.
- when application write/read file to remote folder, application thinks that it is local directory, but it is not truth. This folder has network latency and application may crash.
- very difficult troubleshooting. When problems with file synchronization happen you should check 2 things: port accessibility and file system permissions, if something go wrong with NFS, you should troubleshoot whole protocol - https://tools.ietf.org/html/rfc1813, and samba even more robust.
- intelligent write to server. When you configure file synchronization you can limit synchronization bandwidth, it can prevent server overload.

# ddos
dos - attack from one server
ddos - attack from multiple servers
- system administrators and webservers can block dos attack
- ddos attack can be blocked only by application (for example 2FA)
You can review proxy servers services, they are offers simple tariff plan with 500k ip addresses. Let's say from 500k 1k will be very active, you can detect it fast in your logs, but another ip addressed will request your servers a couple times per day. If you will block 1k active ip addressed it will not resolve the issue.

# monitoring
- every storage service RabbitMQ, MySQL... should be monitored. Script should generate hash, write it, read it and compare it.
- trigger should describe what's wrong and who (percon) should do.
- create app systems based on queue services, that way you can monitor state of business processes

# Testing
- performance testing is also important as functional testing (1, p.226)
- tesing environment should be production-like (1, p. 279)
- create test data with naming conventions (1, p. 337)
- roll-back transactions after test (1, p. 337)
- tightly coupling test is bad (1, p. 339)
- if it is diffucult to prepare data for test, then you need to decompose project (1, p. 339)

# monolithic vs microservice

- monolithic is good at start up for small apps
- move from monolithic to microservice when:
  - developers IDE slow down
  - long time to start up
  - modules conflicts with each other

# Hardware
- do not use exotic hardware, especially network, it is not code, it's impossible to debug hardware blackbox. It will be difficult find tech info.

# Misc
- in enterprice do not send user credentials directly to users, users should request it from helpdesk, otherwise you will be DDOSed.

# External sources + recomended books

1) Continuous Delivery Reliable Software Releases through Build, Test, and Deployment Automation by Jez Humble, David Farley. 2010

2) "Refactoring Databases" and book "Recipes for Continuous Database Integration". Scott Ambler. (reference from 1, p. 329)

3) Microservices patterns with example in Java. 2019. Chris Richardson.

4) Agile Testing, 2009. Lisa Crispin. (reference from 1, p. 84)

5) Agile Samurai, 2010 (reference from Agile Testing, 2009. Lisa Crispin.)

6) Visible Ops Handbook, 2005 (reference from 1, p. 286)

7) M. Burgess, Principles of Network and System Administration: Wiley, 1999. (reference from 8)

8) Site Reliability Engineering, edited by Betsy Beyer, Chris Jones, Jennifer Petoff, and Niall Richard Murphy (O’Reilly). Copy‐right 2016 Google, Inc., 978-1-491-92912-4
